
<head>
    <link rel="apple-touch-icon" sizes="180x180" href="/assets/favicon/apple-touch-icon.png">
    <link rel="icon" type="image/png" sizes="32x32" href="/assets/favicon/favicon-32x32.png">
    <link rel="icon" type="image/png" sizes="16x16" href="/assets/favicon/favicon-16x16.png">
    <link rel="manifest" href="/assets/favicon/site.webmanifest">
</head>

[Back to Homepage](/README.md)

<a href="assets/img/CS 339 Notes.pdf" target="_blank">PDF of class notes.</a>

# CS 497: Advanced Databases

Created: April 5, 2025 12:29 AM
Class: CS 497
Type: Lecture
Reviewed: No

# Lecture 1

## Historical Models

- a
    - One tuple at a time
- Hierarchical Data Model:
    - One tuple at a time
    - A lot of redundant information; if you want to change something, difficult to make sure everything is synchronized (must loop through all to check).
    - Query processing: if you want to find all suppliers that sell batteries, need to read all supplier and every single part that supplier sells (no easy way to go from part to supplier).
- Relational Model: database abstraction to avoid maintenance:
    - Store database in simple data storage
    - Access data through high-level languages
    - Physical storage left up to implementation
- Object-Oriented Model (e.g. JSON)
    - Redundancy issue: if you share the same phone number between work and mobile

# Lecture 2 (4/9/25) -

## a

DBMS Overview:

- SQL query → networking layer → planner → compiler → execution engine → storage manager
    - networking: receives query
    - planner: parses, binding, rewriting, optimization
    - compiler: (not universal)
    - execution engine: scheduling/placement, concurrency control, operator execution, indexes
    - storage manager: storage models, logging/checkpoints

Distributed Query Execution:

- Decouple storage and compute (compute in the cloud, storage is a unified object store). Examples: Snowflake, RDS and S3
- Executing OLAP in distributed is roughly the same as single node
- For each operation, need to worry about where input is coming from and where to send output (e.g. other machine, cached locally)
- Slide 42
    - Worker nodes have their own memory, CPU
    - Worker nodes pull some data from their persistent data store, do computation, and output intermediate data.
    - Shuffle nodes: like peer-to-peer communication (workers exchange data); avoids needing to go back to the persistent data of the worker nodes.
- Types of Data:
    - Persistent Data: tables of the database. Accessible by all workers, assume immutable
    - Intermediate Data:
        - Produced by query operators during execution
        - xx
- Two approaches:
    - Push Query to Data: leaves of query plan are emitting data
        - Send query (or portion of it) to node that contains the data.
        - Perform as much
        - E.g. S3 select: pushes down a filter to the storage layer so you only retrieve some of it.
    - Pull Data to Query: root of query plan calls Next function on children (iterator)
        - 

Choices for System Architecture

- Shared-Nothing Approach:
    - Each DBMS has its own CPU, memory, local disk. They can only communicate with each other via network. Since data is local, access via POSIX APIs.
    - Database is partitioned into disjoint subsets across nodes using some algorithm. Challenge: if adding a new node, need to physically move data between nodes (need to partition again, can be expensive)
    - Harder to scale
    - Potentially better performance and efficiency.
- Shared-Disk (Snowflake uses this):
    - Each node accesses a single logical disk via an interconnect, but also has its own private memory and ephemeral storage. (So there is a single shared disk)
    - Instead of POSIX API, DBMS access disk via a userspace API.
    - Less fine-grained control.
    - May need to pull uncached persistent data from storage layer to compute layer before filtering.

Object Stores: “infinitely scalable”, in the cloud

- Partition the database’s tables (persistent data) into large, immutable files stored in the object store.

<aside>
💡

RISC: reducing everything to minimum functionality and do the rest by building those together. Decompose DBMS into atomic units.

</aside>

Components:

- DBMS catalog: maps data files to objects in your DB.
- Query optimizer:

## Storage

What data “looks like”.

### Storage Models

How a DBMS physically organizes tuples on disk and in memory. Choices:

1. N-ary storage model (NSM) - row; tuples contiguously
    - All attributes of a single tuple contiguously in one page.
    - Good for insert-heavy and OLTP where txns tend to access individual — locality is good.
    - Use the tuple-at-a-time iterator processing model.
    - Tradeoff between page sizes — granularity: if you are only accessing small amounts of data, having large page size is inefficient; limits concurrency. But larger pages means less movement to buffer pool.
    - Recall slot array is pointers to where the tuple begins in the page.
    - If you have a query that wants the sum of one column, you are wasting effort reading only a subset of columns.
    - Difficult to compress if you have different types.
2. Decomposition storage model (DSM) - column
    - Use vectorized processing model.
    - Store using a fixed size so that you can easily get the offset to an element.
    - Maintain a separate file per attribute with a dedicated header area for meta-data about entire column.
    - To deal with variable length fields:
        - Use a dictionary compression to convert repetitive variable-length data into fixed-length values.
        - Use a slot that points to the string data (like tuples in NSM storage)
    
    <aside>
    💡
    
    OLAP queries almost never access a single column in a table by itself. At some point, the query must get other columns and stitch the original tuple back together. To get the storage and execution benefits of the columnar format, we use the hybrid method. 
    
    </aside>
    
3. Hybrid storage model - Partition Attributes Across (PAX)
    - Vertically partitions attributes within a page.
    - Faster processing on columns while retaining spatial locality benefits of rows.
    - Way it’s organized: slide 15
        - Horizontally partition rows into groups, then vertically partition their attributes into columns.
            - Each row group has a header that contains its own meta-data.
            - The row group has header, a0a1a2, b0b1b2, c0c1c2 (rows 1-3 and their columns)
            - If the header is immutable, you might not know how long the header needs to be (e.g. statistics aren’t known until the end), so you can put it in the footer instead.
        - Global header contains the directory which gives offsets so you can jump to location.

<aside>
💡

Recall (from intro notes) why we don’t use OS.

</aside>

### Type Representation

Numbers

- Variable-Precision Numbers
    - E.g. Float, real/double
    - Typically faster because special register instructions.
    - But not guaranteed to be precise.
- Fixed-Precision Numbers
    - E.g. numeric, decimal.
    - Specify the number of decimal digits.
    - Many different implementations, but require a lot of metadata

Null data types:

1. Choice 1: special values - designate a value to represent NULL for a data type
    - Advantage: when scanning for a particular value, if it’s null, you can skip and scan along.
    - Disadvantage: reduces the available space for non-null values.
2. Choice 2: null column bitmap header (more common in header for row-based) - store a bitmap in a centralized header that specifies what attributes are null
    - Disadvantage: have to read header to see if value is there instead of just scan.
3. Choice 3: per attribute null flag - store a flag that makes that a value is null. Note that there must be padding to preserve word alignment.

HTAP Storage Model: 

<aside>
💡

When we insert a tuple, more likely to update it. As it ages, less likely to update and becomes more read heavy.

</aside>

- Store new data in NSM for fast OLTP
- Migrate data to DSM for more efficient OLAP
- Combine query results from both engines to appear as a single logical database to the application.
- Approaches:
    1. Fractured Mirrors: store a second copy of the database in a DSM layout that is automatically updated. NSM is the primary storage (transactions update this first), updates are copied into the DSM mirror at some fixed interval. Analytical queries look at the mirror.
    2. Delta Store: stage updates in an NSM table. Once you get a large enough chunk, migrate updates to DSM data (so columnar is the primary whereas in fractured mirrors, NSM is the primary). Batch large chunks and write them out as a PAX file. 

### Partitioning

Split database across multiple resources (e.g. disks, nodes, processors).

- DBMS executes query fragments on each partition and then combines the results to produce a single answer.
- DBMS can partition physically (shared nothing) or logically (shared disk)

Horizontal Partitioning: split a table’s tuples into disjoint subsets based on a partitioning key and scheme (e.g. hashing, ranges, predicates). 

- You want to choose columns that divides the database equally in terms of size, load, or usage.

### Observations

<aside>
💡

</aside>

# Lecture 3 - Compression

- Datasets have highly skewed distributions for attribute values.
    
    Example: assigning shorter bits for more common
    
- Datasets tend to have high correlation between attributes of the same tuple (zip code to city)

<aside>
💡

Key tradeoff: speed vs. compression ratio

- Compressing reduces DRAM requirements and processing.
- But can be slow to decompress/process.
</aside>

## Background

Want to reduce the size of physical representation so the number of values accessed and processed per unit of computation (e.g. SIMD: single instruction multiple data). Then you can process more of them in parallel. Goals:

1. Produce fixed-length values.
2. Should be lossless (no losing data)
    - Lossy compression: jpeg, mp3
    - Any kind of lossy compression should be performed at the application level.
    - Related idea: approximate query processing. Reading less than the entire dataset during query execution (e.g. approximating the mean of a very large dataset)
3. Postpone decompression for as long as possible during query execution. Late materialization. 

Compression Granularity

1. Block-level: compress a block (e.g. page, tuple) of tuples in a table. 
    - Course granularity
2. Column-level: compress multiple values for one or more attributes stored for multiple tuples (DSM-only).
    - Attributes are probably each all of the same format, so you can do more fine-grained compression.

## Naive Page Compression

Using general-purpose compression algorithm. → Scope of compression is only based on the data provided as input.

Considerations

- Computation overhead
- Compression vs. decompression speed

Example of why this is not great for databases with attributes: prefix codes

- These have variable length, so you can’t use an offset to access a specific element.
- Instead, you have to decompress everything up to this point to get to the index you want.

### Choices for Naive Compression

1. Entropy Encoding: more common sequences use fewer bits to encode. E.g. arithmetic coding.
2. Dictionary Encoding: build data structure that maps data segments to an identifier. E.g. Hoffman coding.

*Note that for these two examples, expensive to decompress because you need to know the codes a priori.* 

Example: MySQL InnoDB Compression

- mod log keeps track of the updates
- Decompress and apply changes
- This makes processing more expensive. You save disk space. You save memory space if you don’t need to modify.
- This saves bandwidth from disk to memory because you are sending compressed.

<aside>
💡

Overall, for naive compression:

- DBMS must decompress first before every read or write.
- Doesn’t consider higher-level knowledge of meaning or semantics of data.
</aside>

## Native Columnar Compression

It would be better if predicates and data are compressed in the same way so then you can evaluate predicate without having to decompress. 

Modify the query so that it works directly on the compressed data. 

- Run-Length Encoding: Compress runs of the same value in a single column into triplets (value, start position in column segment, and number of elements in the run)
    - For it to work well, you want columns to be sorted to maximize compression opportunities. Want long sequences of the same value. Then the triplet size is shorter than the original data so it pays off.
    - Issue: difficult to index because you will have to scan to find the i-th row. Can’t index directly.
- Dictionary Encoding: Replace frequent values with smaller fixed-length codes and maintain a mapping between them. One code per attribute value (so good for if you have lots of repeats).
    - Want both fast encoding and decoding for both point (name = ‘Alice’) and range (age > 20) queries.
    - Dictionary Construction:
        1. All-At-Once: compute the dictionary for all tuples at a given point in time.
            - New tuples get a new dictionary, or all tuples reconstructed.
            - Good for immutable.
        2. Incremental: merge new tuples with an existing dictionary. 
            - Easier for point queries (just use a counter to give new tuples values).
            - Likely requires re-encoding to existing tuples.
            - For range queries, you need to maintain order somehow. E.g. initialize them with spaces in between. Slide 23 for more on order-preserving.

## Intermediate Data