---
layout: post
title: An Integrated Approach To Performance Engineering
---

Performance Engineering is often considered a black art. While there are numerous references on different aspects of performance engineering like testing, optimization, scaling etc,&nbsp; I couldn't find an integrated approach to performance engineering spanning all the key activities.

In this article I wanted to summarize the key activities around performance engineering and how they are all interrelated.

Performance engineering may broadly comprise of 5 key activities:

* <a href="/notes/2015/07/30/integrated-approach-to-performance-engineering#performance-testing">Performance Testing</a>
* <a href="/notes/2015/07/30/integrated-approach-to-performance-engineering#performance-monitoring">Performance Monitoring</a>
* <a href="/notes/2015/07/30/integrated-approach-to-performance-engineering#performance-analysis">Performance Analysis</a>
* <a href="/notes/2015/07/30/integrated-approach-to-performance-engineering#performance-optimization">Performance Optimization / Tuning</a>
* <a href="/notes/2015/07/30/integrated-approach-to-performance-engineering#performance-scaling">Performance Scaling</a>

<img alt="" src="/img/notes/integrated-approach-performance-engineering.png" style="width: 600px; height: 450px;" />

### <a id="performance-testing" name="performance-testing"></a>Performance Testing

Performance tests essentially measure the response time and throughput of the application under different load conditions. There are a number of different load testing configurations which can be used to provide insight into site performance. Generally, we run three different types of tests:

* <strong>Baseline tests</strong> These tests are run with a relatively low amount of traffic in order to obtain some baseline information about site performance. These are useful for tracking general user-facing performance (time to first byte, time for a full page load), and to compare against a higher traffic load test result, as well as to track regressions in the standard case.
* <strong>High traffic tests</strong> Tests with relatively higher traffic are run in order to see when site performance begins to degrade as traffic increases. These tests can give you an idea of how many requests a site can handle before performance deteriorates to an unacceptable degree. At the same time, these types of tests are very good for uncovering bottlenecks in a site; many times issues with underlying services or service integration require a higher load in order to trigger.
* <strong>Targeted tests</strong> Most tests are designed to cover all different request types and page types for a site. Targeted tests take a different approach; they are designed to test one or several specific features or user paths. For example, if you are working to improve performance of a certain page type on your site, you could run a load test which only focuses on that particular area.

Testing can be tied into your development process using a tool such as Jenkins. Tests could be set up to run each time a new release is pushed to the staging environment. Or, if you have sufficient resources, tests could even be run each time new code is pushed to the site’s code repository. The more frequently tests are run, the easier it will be to directly link a change in performance to a specific change on the site. If you go too long between tests, it can become much harder to pinpoint the cause of a performance problem.

Some of the key metrics that you measure during a performance test:

* <strong>Time to first byte</strong> – also referred to as latency in some load testing applications. This is the time from the moment the request was sent till the first response has been received.
* <strong>Page load time</strong> – also referred to as response time in some applications.&nbsp; This is the time from the moment the request was sent till the last response has been received.
* <strong>Requests per second</strong> The number of page requests per second and is an important statistic to look at when planning for traffic loads and infrastructure scaling..
* <strong>Error responses</strong> These are another very important data point, and also the most likely to be ignored. Paying attention to the error responses can give you an idea of where problems may be occurring.

### <a id="performance-monitoring" name="performance-monitoring"></a>Performance Monitoring

While performance test results are focused entirely on the client-side performance, monitoring focuses on what's happening within the application and the servers while the tests are running. Application monitoring and Server monitoring&nbsp; should be automated so that the results are available ever after the tests are finished running. But it can also be useful to manually watch the servers during test runs to see how things are affected and be able to adjust what you are monitoring.

Some of the key metrics that we would recommend monitoring:

* <strong>Application Performance Monitoring</strong> - Use APM tool like NewRelic to monitor the application performance including individual page transaction performance, slow SQL queries, detailed stack traces that can help diagnose specific pain points in code, identifying transactions that are memory / resource hogs.
* <strong>Web servers</strong> Watch overall system load, memory usage, swap usage, network traffic, and disk I/O. Also keep track of things like Apache process count to see if you are approaching the MaxClients setting.
* <strong>Reverse proxies and other caches like memcached</strong> Watch load, network traffic, and caching statistics. Is your cache hit-rate higher or lower than normal? Try to understand why that might be (e.g. a test plan which only hits a very small subset of the site’s pages would likely cause higher cache hit-rates). Watch memory usage and evictions to be sure that the cache isn’t becoming overfilled and forced to delete items before they’ve expired.
* <strong>Database servers:</strong> Watch the server load and connection count. Ensure that the MySQL slow query log is enabled, and watch it for potential query improvements that can be made. You can also watch MySQL statistics directly or with tools such as mysqlreport to watch things like InnoDB buffer usage, lock wait times, and query cache usage. Watch the MySQL process list to see if there are certain queries running frequently or causing waits for other queries.
* <strong>Log monitoring:</strong> Watch the syslog, application log, MySQL error log, Apache logs to see if any errors are being reported. Use an automated log aggregator to pull logs from the servers and store them in a centralized place for analysis.

### <a id="performance-analysis" name="performance-analysis"></a>Performance Analysis

During performance analysis, we integrate the data from the tests and the monitoring systems to identify the key performance bottlenecks in the system. We start with analyzing the response time, throughput and errors captured by the tests and map it to the system state using the monitoring data. Whenever the performance of the system degrades from the expected values, we use the data provided by the monitoring systems to locate the source of the problem. It could be either because of a slow database query or some portion of the application code taking too long a time (maybe waiting on a web service) or the web server having reached the maximum clients or server running out of RAM.

At the end of the analysis, we should have identified the problem that needs to be fixed. Performance problems generally can be fixed in two ways:

* <strong>Optimization:</strong> Optimization problems are those that can be solved by optimizing the code, adding a cache, modifying a database query, tune a server configuration etc. Optimization involves changing the configuration or code without essentially adding new hardware.
* <strong>Scaling:</strong> Scaling problems are essentially problems of high traffic and solved by adding additional hardware. Scaling can either be horizontal scaling (adding new server instances) or vertical scaling (adding more capacity to the existing server).

### <a id="performance-optimization" name="performance-optimization"></a>Performance Optimization

Performance Optimization encompasses many different ways by which the performance of an application can be improved without essentially adding more hardware. And there are many problems like a slow database query or waiting on a web service request which cannot be solved just by adding more hardware. Some of the key things that can be done to improve the performance of an application:

* <strong>Front end optimization</strong>: Use persistent connections, use gzip for transferring compressible resources, leverage browser caching of static assets, serve static content through a CDN, compressing images, serving scaled images, combine and minify CSS and JavaScript files etc
* <strong>Caching</strong>: Use reverse proxy like Varnish to cache web content, use a backend cache like Memcache or Redis to store results from a performance intensive operation
* <strong>Using task queues</strong>: Tasks queues are an efficient and powerful tool for background processing; they allow your application to define tasks, add them to a queue, and then use the queue to process them in aggregate.
* <strong>Optimize your database queries</strong>: Find out the slow queries using the query log and optimize the queries by simplifying the joins, using indexes etc
* <strong>Tuning your Web Server</strong>: Adjust the configuration parameters of your web server like MaxClients, MaxRequestsPerChild etc for optimal performance
* <strong>Tuning your Database Server</strong>: Adjust the configuration parameters of your database server like max_connections, key_buffer and query_cache_size for optimal performance

For more details, check out our article "<a href="http://www.stackdynamix.com/content/performance-tuning-web-applications">Performance Tuning Web Applications</a>".

### <a id="performance-scaling" name="performance-scaling"></a>Performance Scaling

Performance scaling is needed when we are dealing performance problems because of high traffic. Here no amount of optimization is going to help us since the issues are of different nature. Scaling can be addressed in two ways:

* <strong>Vertical Scaling (Scaling up)</strong>: Vertical scaling is achieved by adding extra resources such as more memory or an additional CPU to the existing servers. Scaling vertically, which is also called scaling up, usually requires downtime while new resources are being added and has limits that are defined by hardware.
* <strong>Horizontal Scaling (Scaling out)</strong>: Horizontal scaling is achieved by adding more machines into your pool of resources. An important advantage of horizontal scalability is that it can provide administrators with the ability to increase capacity on the fly.

### The Integrated Approach

The integrated approach to performance engineering is built on two fundamental tenets:

* Performance tests are run frequently both as part of the build process and whenever the deployment configuration is changed.
* The application and the server is monitored continuously during the tests and the historical data is available for analysis.

One of the biggest advantage of this integrated approach is that we have traceability to analyze the impact of each and every change in the system. By running our tests in the context of a monitoring environment, we can easily see the performance impact that our optimization or scaling has achieved. We can always go back to the historical monitoring metrics to check how changing a particular configuration variable or adding new hardware has affected the overall performance of the system.
