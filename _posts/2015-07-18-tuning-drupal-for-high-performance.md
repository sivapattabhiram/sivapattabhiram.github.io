---
layout: post
title: Tuning Drupal For High Performance
---

In the last post <a href="/notes/2015/07/01/performance-tuning-web-applications">"Performance Tuning Web Applications"</a> we looked at 25 different ways by which the performance of a web application can be improved. In this post, lets see how these rules can be applied to Drupal. Out of the box performance of Drupal is not so great. Yet with the right kind of tuning, Drupal transforms itself into one of the fastest CMS.

A good example is the <a href="https://www.drupal.org/">drupal.org</a> website itself. Have a look at the <a href="https://www.drupal.org/metrics">metrics</a> on number of nodes, users and comments created on drupal.org over the last few months and it will give you an idea of the volume that this site is dealing with. Drupal has proved itself to be a preferred choice for large and high traffic websites.

Lets look at how we can optimize a typical default Drupal installation.

* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#use-presistent-connections">Use persistent connections (keep alive)</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#use-gzip-compression">Use gzip compression for transferring compressable responses</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#cacheable-headers">Leverage browser caching of static assets</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#use-cdn">Serve static content through a CDN</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#compress-images">Compress Images</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#serve-scaled-images">Serve scaled images</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#lazy-load-images">Lazy Load Images</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#combine-minify-css-js">Combine and Minify CSS and JavaScript files</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#optimize-css-delivery">Optimize CSS delivery by inlining critical above-the-fold CSS</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#remove-render-blocking-js">Remove render-blocking JavaScript</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#use-reverse-proxy">Use a Reverse Proxy to serve cached content</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#log-syslog">Log application messages to syslog</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#cache-data">Optimize your code by caching data aggressively</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#use-pluggable-caches">Use dedicated and pluggable backend caches</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#cache-warming-strategy">Have a cache warming strategy when using cache extensively</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#use-task-queues">Use task queues</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#use-search-backends">Use dedicated search backends</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#use-ssds">Use SSDs instead of hard disk drives</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#tune-web-server">Tune your web server for high performance</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#tune-database">Tune your database for high performance</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#horizontal-vertical-scaling">Architect your application for both horizontal and vertical scaling</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#terminate-ssl">Terminate SSL in load balancer</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#profile-application">Profile your application performance regularly</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#real-user-monitoring">Monitor real user performance</a>
* <a href="/notes/2015/07/18/tuning-drupal-for-high-performance#performance-testing-ci">Do performance testing as part of CI process</a>

### <a id="use-presistent-connections" name="use-presistent-connections"></a>Use persistent connections (keep alive)

* <a href="http://httpd.apache.org/docs/2.2/mod/core.html#keepalive">Apache KeepAlive, KeepAliveTimeout Directive</a>
* <a href="http://nginx.org/en/docs/http/ngx_http_core_module.html#keepalive_disable">Nginx keepalive_disable, keepalive_requests, keepalive_timeout Directive</a> (enabled by default)

You can use <a href="http://www.giftofspeed.com/check-keep-alive/">Keep-Alive Checker</a> to check if keep-alive is enabled on your server.

### <a id="use-gzip-compression" name="use-gzip-compression"></a>Use gzip compression for transferring compressible responses

For Apache, the default .htaccess file enables gzip on css and js files. For this to work, mod_gzip should be enabled in Apache.

For Nginx, add the following to your nginx.conf

```
gzip on;
gzip_comp_level 2;
gzip_http_version 1.0;
gzip_proxied any;
gzip_min_length 1100;
gzip_buffers 16 8k;
gzip_types text/plain text/html text/css application/x-javascript text/xml application/xml application/xml+rss text/javascript;

# Disable for IE &lt; 6 because there are some known problems
gzip_disable "MSIE [1-6].(?!.*SV1)";

# Add a vary header for downstream proxies to avoid sending cached gzipped files to IE6
gzip_vary on;
```

### <a id="cacheable-headers" name="cacheable-headers"></a>Leverage browser caching of static assets

Drupal sets cacheable headers for all CSS, JavaScript, and images, as well as for cached HTML pages. The HTML max_age value of the Cache-Control header can be set via admin/config/development/performance; assets are set to have an Expires header of two weeks via .htaccess if mod_expires is enabled in Apache. If you’re not using Apache, you’ll need to ensure that you handle cacheable headers for static assets in the web server you’re using.

### <a id="use-cdn" name="use-cdn"></a>Serve static content through a CDN

The <a href="https://www.drupal.org/project/cdn">CDN</a> module provides easy Content Delivery Network integration for Drupal sites. It alters file URLs, so that files are downloaded from a CDN instead of your web server. It provides two modes: "Origin Pull" and "File Conveyor".

### <a id="compress-images" name="compress-images"></a>Compress Images

The best place to compress will be at the time of saving your images inside your image editor.

The <a href="https://www.drupal.org/project/imageapi_optimize">ImageAPI Optimize</a> module can be used to optimize your newly created images (it cannot optimize existing images). The module supports several tools to do this, for different image formats. Some are already present on most systems.

* (PNG) advpng is a part of <a href="http://advancemame.sourceforge.net/comp-readme.html" rel="nofollow">advancecomp</a>. It is available in most distros (<a href="http://packages.debian.org/search?keywords=advancecomp" rel="nofollow">Debian</a>). If you don't have root access to the server you will need to compile it.

* (PNG) <a href="http://optipng.sourceforge.net/" rel="nofollow">OptiPNG</a> is a tool to optimize PNG images. This module uses the default option for a size/execution time trade-off. OptiPNG can be built by <code>./configure;make</code>

* (PNG) <a href="http://pmt.sourceforge.net/pngcrush/" rel="nofollow">PNGCRUSH</a> predecessor where optipng was forked.

* (JPEG) jpegtran is a part of <a href="http://ijg.org/" rel="nofollow">jpeglib</a> and is available in most systems, if not please install the package <code>libjpeg-progs</code>. This tool can remove extra markers from source file.

* (JPEG) <a href="http://lyncd.com/files/imgopt/jfifremove.c" rel="nofollow">jfifremove</a> strip JFIF headers. You need to compile it: <code>gcc -o jfifremove jfifremove.c</code>
 
<a href="http://www.drupal.org/project/imageapi_optimize">ImageApi Optimize</a> supports progressive JPEGs through an option in jpegtran settings.

### <a id="serve-scaled-images" name="serve-scaled-images"></a>Serve Scaled Images

You can optimize images for bandwidth using image styles, which ensure the images are scaled or cropped to the size they will be served at. Drupal Image module automatically creates copies of images based on configured image styles; for example, thumbnail, medium, large, etc.

### <a id="lazy-load-images" name="lazy-load-images"></a>Lazy Load Images

* <a href="https://www.drupal.org/project/lazyloader">Image Lazyloader Module</a>
* <a href="https://developers.google.com/speed/pagespeed/module/filter-lazyload-images">PageSpeed LazyLoad Images Filter</a>

### <a id="combine-minify-css-js" name="combine-minify-css-js"></a>Combine and Minify CSS and JavaScript files

Drupal core supports aggregating CSS and JavaScript files. But the core aggregation has few problems. First while aggregating, it considers only one page at a time resulting in duplicate css files getting loaded on multiple pages. Another problem is that when the cache is cleared, the aggregated files are deleted. If a static page still holds a reference to the deleted aggregated files, then we will have issues in the page rendering.

<a href="http://drupal.org/project/advagg">Advanced CSS/JS Aggregation</a> module solves the above issues and also supports minification of CSS and JS files. AdvAgg comes with one module, and seven submodules: advagg_js_compress, advagg_validator, advagg_js_cdn, advagg_mod, advagg_css_cdn, advagg_bundler, advagg_css_compress, advagg. The module is being used in <a href="https://www.drupal.org/">drupal.org</a> for JSS/CS aggregation and minification.

### <a id="optimize-css-delivery" name="optimize-css-delivery"></a>Optimize CSS delivery by inlining critical above-the-fold CSS

The best option to achieve this is by using Google <a href="https://developers.google.com/speed/pagespeed/module">PageSpeed Module</a> which is available as a server module for both Apache and Nginx. The <a href="https://developers.google.com/speed/pagespeed/module/filter-prioritize-critical-css">Prioritize Critical CSS</a> filter in PageSpeed does inlining of critical above-the-fold CSS by parsing the CSS and replacing it with inlined CSS that contains only the rules used on the page. It also collects all CSS tags and appends them to the HTML in the same order they were found. This will make all style rules available after page load.

### <a id="remove-render-blocking-js" name="remove-render-blocking-js"></a>Remove render-blocking JavaScript

PageSpeed <a href="https://developers.google.com/speed/pagespeed/module/filter-js-defer">Defer Javascript</a> filter tries to defer JavaScript execution until page load. It defers this by changing the type and src attributes of &lt;script&gt; elements on the HTML page to pagespeed_orig_type and pagespeed_orig_src respectively. It also adds a new type attribute whose value is set to text/psajs. A window.onload handler is added to the HTML, which executes all the deferred scripts. defer_javascript doesn't defer a script tag if it has the pagespeed_no_defer attribute. This is useful when a script tag needs to be executed while loading the page.

<a href="http://drupal.org/project/advagg">Advanced CSS/JS Aggregation</a> modules 'Modifier submodule' can be used to move JS to the footer, add defer tags to all JS, inline JS for given paths and remove unused Javascript tags.

### <a id="use-reverse-proxy" name="use-reverse-proxy"></a>Use a Reverse Proxy to serve cached content

<a href="https://www.varnish-cache.org/">Varnish</a> has generally become the reverse proxy of choice for Drupal. Caching static content items like images and JavaScript is relatively easy and can be done with minimal configuration. You can also configure Varnish to cache full pages served by Drupal. You can closely integrate Varnish and Drupal such that when an object is edited, Drupal can immediately purge pages containing that object out of Varnish’s cache to prevent stale content from being served. Some of the contributed modules to help with Varnish:

* <a href="https://drupal.org/project/varnish">Varnish HTTP Accelerator Integration</a>
* <a href="https://drupal.org/project/expire">Cache Expiration</a>

### <a id="log-syslog" name="log-syslog"></a>Log application messages to syslog

The Syslog module logs events by sending messages to the logging facility of your web server's operating system.

* <a href="https://www.drupal.org/documentation/modules/syslog">Syslog: using the operating system's logging facility</a>

### <a id="cache-data" name="cache-data"></a>Optimize your code by caching data aggressively

Drupal has a built in <a href="https://api.drupal.org/api/drupal/7/search/cache">Cache API</a>, which allows module developers to save output of the expensive parts of code to be re-used in future.

Many of the core modules use Cache API extensively to cache data.

Blocks and page caching can be enabled at Administer » Configuration » Development » Performance.

<a href="https://drupal.org/project/views">Views</a> ships with a built-in time-based caching system. It supports two types of caching<br />
a) Query results: This caches only the results of the main listing query configured in the View, using the query itself as the cache key.<br />
b) Rendered output: This caches the rendering of the items in the View, once the results have been retrieved.

Additional cache support is provided by the following modules:

* <a href="https://drupal.org/project/views_content_cache">Views Content Cache</a>: Update each view only when its constituent element have been updated.
* Cache entities with <a href="https://drupal.org/project/entitycache">Entity Cache</a>.
* Cache entity view modes with <a href="https://drupal.org/project/display_cache">Display Cache</a>.
* Cache Panels content with<a href="https://drupal.org/project/panels_content_cache"> Panels Content Cache</a> or <a href="https://drupal.org/project/panels_hash_cache">Panels Hash Cache</a>.
* Cache components with logged-in users via <a href="https://drupal.org/project/authcache">Authenticated User Page Caching (Authcache)</a>.

### <a id="use-pluggable-caches" name="use-pluggable-caches"></a>Use dedicated and pluggable backend caches

The default cache implementation of Drupal uses the database for storing cache entries which is not very efficient.

Alternate cache implementations are available:

* <a href="https://www.drupal.org/project/memcache">Memcache API and Integration</a>
* <a href="https://www.drupal.org/project/redis">Redis</a>
* <a href="https://www.drupal.org/project/filecache">File Cache</a>

### <a id="cache-warming-strategy" name="cache-warming-strategy"></a>Have a cache warming strategy when using cache extensively

<a href="https://www.drupal.org/project/cache_warmer">cache_warmer</a> is a drush command that hits a set of URIs of a drupal site based on the freshness of the content. The main purpose is to offer a complete setup for running a mostly cached drupal site without having to deal with expiration logic and instead use microcaching.

<a href="https://www.drupal.org/project/drush_ecl">Drush Entity Cache Loader</a> is another drush plugin that warms up your content caches and can help content load faster by running through and loading any/all entity content. Once entity content is loaded, components of the content (or all of it if it can use Entitycache) can get cached thus making the latter content loads much faster. By providing an argument, you can also cache all entities of a certain type (like node, user, taxonomy_term, etc).

### <a id="use-task-queues" name="use-task-queues"></a>Use task queues

Drupal core ships with a robust <a href="https://api.drupal.org/api/drupal/modules%21system%21system.queue.inc/group/queue/7">Queue API</a>, defaulting to MySQL and with contributed projects providing support for Redis, Beanstalkd, and others.

### <a id="use-search-backends" name="use-search-backends"></a>Use dedicated search backends

* <a href="https://www.drupal.org/project/search_api_solr">Search API Solr Search</a> - This module provides a Solr backend for the Search API module.
* <a href="https://www.drupal.org/project/search_api_elasticsearch">Search API ElasticSearch</a> - This module provides an Elasticsearch backend for the Search API module.

### <a id="use-ssds" name="use-ssds"></a>Use SSDs instead of hard disk drives

Managed Drupal Hosting Providers like Pantheon use SSDs for delivering high performance.

* <a href="https://pantheon.io/blog/why-pantheon-just-got-40-faster">Why Pantheon Just Got 40% Faster</a>

### <a id="tune-web-server" name="tune-web-server"></a>Tune your web server for high performance

Popular web server configurations for Drupal

* Apache Prefork MPM with mod_php
* Apache Worker MPM with PHP-FPM
* Nginx with PHP-FPM

For Apache Prefork MPM, the following parameters need to be tuned properly based on system memory.

```
StartServers
MinSpareServers
MaxSpareServers
ServerLimit
MaxClients
MaxRequestsPerChild
```

For Apache Worker MPM, the concurrency parameters are:

```
StartServers
MaxClients
MinSpareThreads
MaxSpareThreads
ThreadsPerChild
MaxRequestsPerChild
```

Nginx has no MaxClients directive, but the worker_connections option in the events block serves a similar purpose. This limit is per-worker. So, with worker_connections set to 1024, and worker_processes set to 8, you allow a maximum of 8,192 simultaneous connections.

Detailed tuning instructions are beyond the scope of this post. We will address it in a separate article.

### <a id="tune-database" name="tune-database"></a>Tune your database for high performance

Useful tuning tips from <a href="http://chimera.labs.oreilly.com/books/1230000000845">High Performance Drupal Book</a>:

* <u>MySQL Global Configuration key parameters</u>: Drupal doesn’t require tuning of many nonstandard global options. Those you may want to consider include: max_connections, key_buffer and query_cache_size
* <u>Per-Thread Configuration key parameters</u>: Many of the quality configuration templates, such as those from the <a href="https://iuscommunity.org/pages/About.html">IUS RHEL/CentOS repositories or Percona Tools for MySQL</a> have good defaults for these values. The only per-thread settings we often recommend tweaking for a Drupal website are those affecting temporary tables: tmp_table_size/max_heap_table_size
* <u>Storage Engine Configuration</u>: Unlike most of the per-thread configuration, correctly configuring InnoDB is extremely important. In particular, the InnoDB buffer size and transaction flush method are possibly the most important tunables for a Drupal installation.
* <u>Replication</u>: Most every production deployment will need a replicated slave, at the very least for failover during crashes and/or updates. Drupal-specific considerations: Replication type, Slave lag and Read-only queries.
* Useful tools from <a href="http://www.percona.com/software/percona-toolkit">Percona Toolkit</a>: pt-query-digest, pt-table-checksum, pt-online-schema-change, pt-index-usage.
* Useful tools from <a href="http://code.openark.org/forge/openark-kit">openark kit</a>: oak-chunk-update, oak-kill-slow-queries, oak-purge-master-logs, oak-security-audit, oak-show-limits.
* <a href="http://blog.codenode.com/2015/01/hack-mysql-has-been-retired.html">mysqlreport</a> should be run on every MySQL server at least once. This utility will print out current usage information for the query cache, key buffer, and InnoDB buffer pool, as well as detailed InnoDB statistics and much more.
* While tuning is important, it often has nowhere near the impact of actually fixing a poorly performing query.

### <a id="terminate-ssl" name="terminate-ssl"></a>Terminate SSL in load balancer

SSL communications can cause a significant amount of CPU overhead, both during the initial handshake (when keys are exchanged) and the encryption/decryption of packets sent over the connection. This can have an impact on the performance of back-end servers, and many load balancers offer features to take over some of the work.

### <a id="horizontal-vertical-scaling" name="horizontal-vertical-scaling"></a>Architect your application for both horizontal and vertical scaling

A typical Drupal architecture with limited horizontal scaling:

<img alt="" src="/img/notes/scaling-drupal-illustrations1.png" style="width: 600px; height: 600px;" />

Drupal Architecture supporting both horizontal and vertical scaling:

<img alt="" src="/img/notes/scaling-drupal-illustration2.png" style="width: 600px; height: 600px;" />

### <a id="profile-application" name="profile-application"></a>Profile your application performance regularly

* <a href="http://newrelic.com/php/drupal">New Relic Application Performance Monitoring</a> provides deep insight into what’s happening in your Drupal application. Viewing slow SQL queries and using Web Transaction Traces, you can see which functions are the slowest in your application.
* <a href="http://drupal.org/project/devel">Devel</a> module provides a number of debugging tools for developers. Amongst these are tools for monitoring page timing, memory consumption, and the query log, and for XHProf profiling.
* <a href="http://xdebug.org/">XDebug</a> - You can profile your application using XDebug. XDebug profiling generates cachegrind files, which can then be viewed in an interface such as KCacheGrind or or webgrind, which is written in PHP and has a web interface.
* <a href="http://pecl.php.net/package/xhprof">XHProf</a> is a hierarchical profiler for PHP. Using XHProf, you can evaluate your website's code performance and identify functions that may be running slowly or using an excessive portion of your server resources.
* <a href="https://en.wikipedia.org/wiki/Strace">strace</a> is a great tool for looking at what happens at the system level.

### <a id="real-user-monitoring" name="real-user-monitoring"></a>Monitor real user performance

* <a href="http://newrelic.com/browser-monitoring">NewRelic Browser Monitoring</a>
* <a href="https://www.pingdom.com/product/performance-monitoring">Pingdom Real User Experience Monitoring</a>
* <a href="https://support.google.com/analytics/answer/1205784?hl=en">Google Analytics Site Speed</a>

### <a id="performance-testing-ci" name="performance-testing-ci"></a>Do performance testing as part of CI process

* <a href="https://blazemeter.com/blazemeter-new-jenkins-plugin">BlazeMeter Jenkins Plugin</a> - The plugin allows users to run Blazemeter cloud based load testing as part of the Jenkins build cycle

