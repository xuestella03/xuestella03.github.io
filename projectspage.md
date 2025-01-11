<head>
    <link rel="apple-touch-icon" sizes="180x180" href="/assets/favicon/apple-touch-icon.png">
    <link rel="icon" type="image/png" sizes="32x32" href="/assets/favicon/favicon-32x32.png">
    <link rel="icon" type="image/png" sizes="16x16" href="/assets/favicon/favicon-16x16.png">
    <link rel="manifest" href="/assets/favicon/site.webmanifest">
</head>

[Back to Homepage](/README.md)


## Projects

*Some projects were completed as part of coursework. To maintain academic integrity, I will not be linking the repositories. However, I am happy to discuss them, including the skills and techniques applied, upon request.*

**Advertisement Conversion Modeling** - <a href="https://github.com/xuestella03/Advertisement-Conversion">GitHub Repository</a>

*Python, XGBoost, GridSearchCV, pandas, scikit-learn*
- Implemented functions to clean conversion and non-conversion datasets using Pandas dataframes. Performed data analysis by writing functions to compare continuous and categorical variables (e.g., conversion by zip code).
- Built one classification model using XGBoost and another using GridSearchCV to predict the conversions and non-conversions of advertisements given features such as zip code, browser, etc.
- Trained and tested the classifier by splitting conversion/non-conversion datasets. Achieved ROC AUC score of 0.88.

**Fridge and Grocery Application in Amazon Web Services** - [Link to Webpage](/fridge.md)

*AWS (S3, RDS, Lambda), RESTful APIs, Python, JavaScript*
- Built an application allowing users to keep track of items in their fridge and create grocery lists based on ingredients needed for uploaded PDF recipes. Extracted text from images or non-PDF recipe formats. 
- Used S3 to store PDFs and RDS to track users following practices for RESTful APIs. Added user authentication. 
- Wrote client-side Python code and AWS Lambda functions to create a “serverless” and event-driven architecture.
- Created a similar project with a web server tier in JavaScript using Node.js and Express frameworks. Applied asynchronous execution programming style. 

**Python Network Scanning Tool** - <a href="https://github.com/xuestella03/Domain-Scanner">GitHub Repository</a>

*Python, Security Protocols (DNS, TLS, HTTP, HSTS), nmap, OpenSSL, nslookup*
- Developed a command-line tool to analyze web domains and evaluate network characteristics and security features. 
- Automated the retrieval of network data such as IP addresses, HTTP server details, TLS versions, and root certificate authorities using utilities such as OpenSSL and nslookup.
- Implemented a JSON reporting framework to present results.
- Ensured portability by managing third-party dependencies and creating a distribution-ready package.

**Predictive Modeling of Stock Market Movement** - <a href="https://github.com/xuestella03/Stock-Market">GitHub Repository</a>

*Python, Random Forest Classifiers, yfinance, scikit-learn*
- Used Random Forest classifiers to predict daily movements of a given stock or index using the opening and closing price, the volume traded, and the highest and lowest price.
- Used the mean reversion theory by adding additional predictors including the ratio between today's closing price and the rolling average over varying periods of time. 
- Backtested the model using growing amounts of data to predict subsequent periods. 
- Analyzed market sentiment from news data and included it as a predictor.

**Derivative Pricing and Financial Modeling** - <a href="https://github.com/xuestella03/Options-Pricer">GitHub Repository</a>

*Python, Financial Theory, Stochastic Volatility, numpy, matplotlib*
- Developed an options pricing model using Black-Scholes to calculate the prices of European options. 
- Calculated the Greeks to assess the sensitivity of option prices to market factors.
- Improved the model to account for volatility fluctuations using Heston's Model. 
- Implemented a Monte Carlo simulation to predict portolio values over time. Analyzed historical data and applied Cholesky decomposition to simulate correlated returns. 
- Interpreted simulation results by computing measures such as value at risk, variance, and confidence intervals. 

**Asteroid Game** - <a href="https://github.com/xuestella03/Asteroid-Game">GitHub Repository</a>

*Unity, C#*
- Developed a flight-simulator type 3D game in Unity.
- Implemented flight controls and projectile shooting. Added enemy objects and tracking to shoot projectiles at player.
- Used collision mechanics to add shooting mechanisms.
- Added scoring and sound effects.

**Jointed Assemblies with Shading and Lighting** - <a href="https://github.com/xuestella03/Computer-Graphics-Project">GitHub Repository</a>

*OpenGL, JavaScript, HTML, Shading and Lighting Techniques*
- Implemented several jointed assemblies in 3D via the vertex buffer object.
- Allowed users to navigate 3D space using keyboard and mouse inputs.
- Implemented Phong and Goraud shading and Phong and Blinn-Phong lighting with variable parameters (i.e. RGB values for the ambient, diffuse, and specular terms).
- Added options to change materials.
- *Some commentary and stub code is provided by Professor Jack Tumblin.*

**Operating System Projects in the Nautilus Kernel** 

*C, Nautilus Kernel, Operating Systems*
- Implemented schedulers including FIFO, non-preemptive shortest job first, preemptive shortest remaining time first, preemptive static priority, round robin, and stride.
- Prevented concurrency issues by using the spinlock, mutex, and semaphore implementations of ring buffer concurrency that resolved issues with both threads and interrupts. 
- Developed a device driver for a parallel port and a GPU.
- Implemented virtual address spaces using x86 paging. Allowed the kernel to manipulate the address space using regions; wrote functions to add/remove/move regions and alter region permission. Handled requests such as switching to and from an address space, invoking exceptions, and adding/removing threads.

**Navigation using OpenStreetMap** 

*C++* 
- Developed a program to perform basic navigation given a map from OpenStreetMap.
- Implemented a graph class in C++ and used it to read a map by importing nodes and paths.
- Used binary search to output information about the map.
- Implemented Dijkstra's Algorithm to navigate the map.

**Simple Structured Query Language**

*C*
 - Implemented a simple version of SQL. 
- Wrote a lexical analyzer turning SQL inputs into storeable tokens. Implemented a parser to enforce syntax rules and an analyzer to enforce semantic rules, detecting any grammatical or conceptual errors in user input. 
- Wrote a program to execute and output results of a query, respecting order, operators, functions, and limits. 