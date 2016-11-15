---
layout: post
title: Performance Tuning Web Applications
---

Performance tuning of web applications is a complex and continuous process which could vary potentially from one application to another. While there are numerous articles in the web on specific topics like frontend performance, database optimization etc or on specific platforms like Drupal, I could not find a consolidated check list of best practices that can be applied across web applications. The goal of this article is to summarize the different ways by which the performance of a web application can be improved based on knowledge base and tools available in the web.

In another series to follow, we will specifically look into how these principles can be applied to specific platforms.

Overall I have collected about 25 ways to improve the performance of a web application. Click on any of these rules to jump to the respective section.

* <a href="/notes/2015/07/01/performance-tuning-web-applications#use-presistent-connections">Use persistent connections (keep alive)</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#use-gzip-compression">Use gzip compression for transferring compressable responses</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#cacheable-headers">Leverage browser caching of static assets</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#use-cdn">Serve static content through a CDN</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#compress-images">Compress Images</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#serve-scaled-images">Serve scaled images</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#lazy-load-images">Lazy Load Images</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#combine-minify-css-js">Combine and Minify CSS and JavaScript files</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#optimize-css-delivery">Optimize CSS delivery by inlining critical above-the-fold CSS</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#remove-render-blocking-js">Remove render-blocking JavaScript</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#use-reverse-proxy">Use a Reverse Proxy to serve cached content</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#log-syslog">Log application messages to syslog</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#cache-data">Optimize your code by caching data aggressively</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#use-pluggable-caches">Use dedicated and pluggable backend caches</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#cache-warming-strategy">Have a cache warming strategy when using cache extensively</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#use-task-queues">Use task queues</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#use-search-backends">Use dedicated search backends</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#use-ssds">Use SSDs instead of hard disk drives</a>
* <a href="/notes/2015/07/01/performance-tuning-web-applications#tune-web-server">Tune your web server for high performance</a>
* <a href="http://localhost/stackdynamix/content/performance-tuning-web-applications#tune-database">Tune your database for high performance</a>
* <a href="http://localhost/stackdynamix/content/performance-tuning-web-applications#horizontal-vertical-scaling">Architect your application for both horizontal and vertical scaling</a>
* <a href="http://localhost/stackdynamix/content/performance-tuning-web-applications#terminate-ssl">Terminate SSL in load balancer</a>
* <a href="http://localhost/stackdynamix/content/performance-tuning-web-applications#profile-application">Profile your application performance regularly</a>
* <a href="http://localhost/stackdynamix/content/performance-tuning-web-applications#real-user-monitoring">Monitor real user performance</a>
* <a href="http://localhost/stackdynamix/content/performance-tuning-web-applications#performance-testing-ci">Do performance testing as part of CI process</a>


### <a id="use-presistent-connections" name="use-presistent-connections"></a>Use persistent connections (keep alive)

HTTP keep-alive allows the same TCP connection for HTTP communication instead of opening a new connection for each new request. ​​All modern browsers use persistent connections as long as the server is willing to cooperate. Check your application and proxy server configurations to make sure that they support Keep-Alive.

The ability to serve multiple files using the same connection can reduce latency and allow web pages to load faster. HTTPS connections in particular are very resource intensive. So it is highly recommended to enable Keep-Alive for HTTPS connections.

### <a id="use-gzip-compression" name="use-gzip-compression"></a>Use gzip compression for transferring compressible responses

Compression of resources by your web server reduces response times by reducing the size of the HTTP response. Gzipping as many file types as possible is an easy way to reduce page weight and accelerate the user experience.

Gzip is the most popular and effective compression method currently available and generally reduces the response size by about 70%.

### <a id="cacheable-headers" name="cacheable-headers"></a>Leverage browser caching of static assets

All server responses should specify a caching policy to help the browsers determine if and when it can reuse a previously fetched response. When the server returns a response it must provide the Cache-Control and ETag headers:

* <strong>Cache-Control</strong> defines how, and for how long the individual response can be cached by the browser and other intermediate caches.
* <strong>ETag</strong> provides a revalidation token that is automatically sent by the browser to check if the resource has changed since the last time it was requested.

### <a id="use-cdn" name="use-cdn"></a>Serve static content through a CDN

Serve static content&nbsp;like images, CSS and JavaScript through Content delivery networks. Configuring a site to use CDN is as simple as rewriting all the urls of static assets to point to the CDN. Using CDN has two major benefits. First, they allow files to be served from a location close to the visitor requesting a site, dramatically reducing the latency of those requests. The second benefit is they reduce the number of requests to your own infrastructure, freeing up bandwidth and server resources to serve only uncached requests that can’t be handled by the CDN.

CDNs can provide an amazing performance boost to your site with very little configuration overhead.

### <a id="compress-images" name="compress-images"></a>Compress Images

Use JPEG if you are optimizing a photo, screenshot, or a similar image asset. JPEG uses a combination of lossy and lossless optimization to reduce filesize of the image asset. Try several JPEG quality levels to find the best quality vs. filesize tradeoff for your asset.

Use Progressive JPEGs. The way progressive JPEGs work is that they store several scans of an image, all of increasing quality. When a browser displays them progressively, the result is a perception of faster loading times. The user actually gets to see something on the screen, even if data is still taking a long time to come back from the server.

Most of the JPEG files are in baseline format because popular image-editing tools are set to baseline as a default. A good number of image-optimization tools exist in the market to convert your images into progressive JPEGs.

Use PNG if you need to preserve fine detail with highest resolution. PNG does not apply any lossy compression algorithms beyond the choice of the size of the color palette. As a result, it will produce the highest quality image, but at a cost of significantly higher filesize than other formats.

Convert non-transparent images to JPEGs.

### <a id="serve-scaled-images" name="serve-scaled-images"></a>Serve Scaled Images

Resize images on the server and ensure that we are not sending any more pixels than needed to display the image in the browser. Never scale images in HTML.

Further note that the number of extra pixels sent is a function of the natural size of the image. So when you send an image of natural size 110x110 to a browser to display the image at 100x100 we are sending 110x110 - 100x100 = 2100 extra pixels. Whereas when you send an image of natural size 810x810 to a browser to display the image at 800x800 we are sending 810x810 - 800x800 = 16100 pixels. So you got to be very careful that your large images in particular are scaled and delivered as close as possible to their display size.

### <a id="lazy-load-images" name="lazy-load-images"></a>Lazy Load Images

Lazy Loading Images is another technique wherein we defer loading of images until they become visible in the client's viewport or the page's onload event fires. This technique improves the load times of pages with many images.

### <a id="combine-minify-css-js" name="combine-minify-css-js"></a>Combine and Minify CSS and JavaScript files

Combining CSS and JavaScript files helps to reduce the number of HTTP requests to the server. On most sites, the major component of download time is not the HTML file itself, but the number of subsequent HTTP requests to load the page's assets - CSS, JavaScript, images, etc. Reducing the number of HTTP requests will make the page load faster. It takes substantially less time to server one 60K file than it does three 20K files and a lot less than it does six 10K files.

You should also minify your HTML, CSS, and JavaScript resources. Minification removes unnecessary or redundant data without affecting how the resource is processed by the browser - e.g. code comments and formatting, removing unused code, using shorter variable and function names, and so on.

### <a id="optimize-css-delivery" name="optimize-css-delivery"></a>Optimize CSS delivery by inlining critical above-the-fold CSS

This is one of the key pagespeed rules and offers a great boost in performance if implemented properly.&nbsp; Before the browser can render content it must process all the style and layout information for the current page. As a result, the browser will block rendering until external stylesheets are downloaded and processed, which may require multiple roundtrips and delay the time to first render.

If the external CSS resources are small, you can insert those directly into the HTML document (inlining). In the case of a large CSS file, you will need to identify and inline the CSS necessary for rendering the above-the-fold content and defer loading the remaining styles until after the above-the-fold content.

### <a id="remove-render-blocking-js" name="remove-render-blocking-js"></a>Remove render-blocking JavaScript

JavaScript resources are parser blocking by default. When a Browser is building the DOM tree by parsing the HTML markup and encounters a script it has to stop and execute it before it can continue parsing the HTML. In the case of an external script the parser is also forced to wait for the resource to download, which may incur one or more network roundtrips and delay the time to first render of the page.

Scripts that are necessary to render page content can be inlined to avoid extra network requests, however the inlined content needs to be small and must execute quickly to deliver good performance. Scripts that are not critical to initial render should be made asynchronous or deferred until after the first render.

### <a id="use-reverse-proxy" name="use-reverse-proxy"></a>Use a Reverse Proxy to serve cached content

Reverse proxies sit before your web server and can be configured to intercept all traffic before it hits your server. Reverse proxies with built-in caches can improve site performance by caching content generated by the web server. When the reverse proxy has a valid item in its cache, it will serve that item from the cache immediately with no backend request to the web server. When the reverse proxy does not have an item cached, or has an out-of-date item cached, it will make a request to the web server for the item, store it in the local cache if it’s cacheable, and then return it to the client.

<a href="https://www.varnish-cache.org/">Varnish</a> and <a href="http://nginx.org/en/">Nginx</a> are two of the most popular reverse proxies in use.

### <a id="log-syslog" name="log-syslog"></a>Log application messages to syslog

Every application needs to have a robust logging policy where alerts, notices and error conditions are logged in a persistent store. If you are logging your application messages into the database shared by other components, you need to consider moving it to syslog. As the application scales up, logging application messages could result in a large number of database writes and degrading the performance. Switching to syslog allows all messages to be logged by the operating system rather than the database, which can help to reduce overall load on the database server.

### <a id="cache-data" name="cache-data"></a>Optimize your code by caching data aggressively

A common way to improve the performance of your application is to use an object cache to speed-up expensive operations by performing it once and persisting the result between HTTP requests. The data can come from any slow source - 3rd Party APIs, Filesystems and more often, the data is coming from your database.

Ideally the database query cache should handle it but there are limitations. For example, MySQL completely erases its query cache after every INSERT and UPDATE operations making it useless for tables even with moderate amout of database writes.

Cache data aggressively - make caching a design pattern consistently applied across your application rather than an afterthought once performance issues surfaces.

### <a id="use-pluggable-caches" name="use-pluggable-caches"></a>Use dedicated and pluggable backend caches

The choice of your cache store is also important from the performance and scalability perspective.

Use a dedicated cache server like Memcached or Redis for caching data. The advantage of using a dedicated object cache layer is that with simple key hashing, you can distribute the cached objects across multiple servers, allowing you to use memory from every machine in your Memcached or Redis cluster.

Abstract your dependency on a particular caching system by using a cache api. As the application scales and as new technology evolves, you may have to move to a more faster cache implementation. Using pluggable backend caches helps you to switch to different cache systems easily.

### <a id="cache-warming-strategy" name="cache-warming-strategy"></a>Have a cache warming strategy when using cache extensively

If you are using cache extensively, it may take quite a while for cached data to get rebuilt whenever caches are cleared (while restarting your server or cache backend). The first users hitting the site afterwards will experience slow page-load times. If you want to avoid it, you need to look at cache warming / priming strategies to rebuild the cache content after any clearing happens. Caches like Redis have built in persistence and automatically does this for you.

### <a id="use-task-queues" name="use-task-queues"></a>Use task queues

Tasks queues are an efficient and powerful tool for background processing; they allow your application to define tasks, add them to a queue, and then use the queue to process them in aggregate. Task queues are most useful when you have expensive operations triggered by actions on the site. For example, creating a new post may require updating the search index, sending email notifications to multiple recipients and clearing various caches. Instead of doing all this work during post update, you can create a task queue and push all these tasks into it. This has the advantage that expensive processing is taken out of the critical path making the application more responsive for the user.

### <a id="use-search-backends" name="use-search-backends"></a>Use dedicated search backends

When the application uses the database queries to search for content, it can put enormous stress on the database server for high traffic sites. Rather use a dedicated search backend like Solr to index the content and do search operations. This removes search load from the database and offers many more advanced search features.

### <a id="use-ssds" name="use-ssds"></a>Use SSDs instead of hard disk drives

SSDs may cost more but using them in your server hardware will give exceptionally good performance.

### <a id="tune-web-server" name="tune-web-server"></a>Tune your web server for high performance

You web server plays a critical part in the performance of your applications. You need to tune it regularly to get the optimum performance out of it. To start with, some of the key aspects you need to consider:

* Choice of web server: Apache or Nginx
* Nginx as a reverse proxy to Apache
* Using FastCGI over mod_php
* Using FastCGI caching
* Multi Process Modules (MPMs) in Apache - Prefork or Worker
* Enabling only the modules needed
* Concurrency settings
* Logging levels

### <a id="tune-database" name="tune-database"></a>Tune your database for high performance

There is still always a huge scope for optimization, both in the configuration of your DB server and in queries themselves. Here are some of areas to consider for MySQL server:

* Selecting the right storage engine
* Tuning Table Cache, Thread Caching and Per-Session Buffers
* Tuning InnoDB buffers and caches
* Configuring Query Cache
* Optimizing your SQL using EXPLAIN, slow query log and indexing
* Using Replication
* Partitioning and Sharding tables

### <a id="horizontal-vertical-scaling" name="horizontal-vertical-scaling"></a>Architect your application for both horizontal and vertical scaling

Vertical scaling is achieved by increasing your server capacity whereas horizontal scaling is achieved by spawning new server instances. If you application has to eventually handle high load, you need to architect it with both horizontal and vertical scaling in mind. The key ingredients of such architecture would be:

* Load balancer connected to one of more web servers
* Mulitple web server instances that can be autoscaled on demand
* Database with a Master / Slave configuration on a separate instances
* File Storage shared by Multiple Web Servers (GlusterFS)
* Cache servers that can be scaled to run on multiple servers

You can start with a minimal configuration (1 Load Balancer, 1 Web Server using Gluster Brick, 1 DB Server) and scale when needed. The important thing is that the architecture should be ready for scaling when needed.

### <a id="terminate-ssl" name="terminate-ssl"></a>Terminate SSL in load balancer

SSL communications can cause a significant amount of CPU overhead, both during the initial handshake (when keys are exchanged) and the encryption/decryption of packets sent over the connection. This can have an impact on the performance of back-end servers, and many load balancers offer features to take over some of the work.

### <a id="profile-application" name="profile-application"></a>Profile your application performance regularly

Profiling your application regularly helps you to understand the time taken by different components of your code. Modern Application Performance Monitoring tools like NewRelic are easy to install and add negligible overheads to performance. The transaction trace features of APM tools gives you complete visibility on all the functions executed by your application sorted by their performance impact.

### <a id="real-user-monitoring" name="real-user-monitoring"></a>Monitor real user performance

Real user monitoring (RUM) helps you to collect performance metrics from actual visits to sites. While RUM tends to be dominated by commercial Software as a Service (SaaS) solutions, there are open source alternatives such as Jiffy (opensourced by Netflix) and free commercial alternatives such as the page timing feature of Google Analytics. Data from RUM needs to be analyzed regularly to understand your visitors and actual load time in user browsers.

### <a id="performance-testing-ci" name="performance-testing-ci"></a>Do performance testing as part of CI process

Performance issues needs to be identified as early as possible in the application development lifecycle. One of the key principles of agile development is to do Continuous Integration and run a set of unit and integration tests for every change committed. Performance tests should be part of the CI process so that any new code that has performance impact can be identified early and fixed.
