---
layout: post
title: High Performance Drupal Book
---

<img alt="" src="/img/notes/high-performance-drupal-book.jpg" style="width: 300px; height: 394px;" />

Recently I was working on optimizing the performance of a large Drupal site. I was looking for a good book on Drupal performance both to reinforce my understanding and also to learn some new stuff. "High Performance Drupal" from O'Reilly is an excellent primer and reference on developing high performance Drupal web sites.

Here are my key notes to serve as a reference - for more details on each point, I would suggest you to refer to the book.

### 1 - Establishing a Performance Baseline

* Measure and record the current site performance. This is your “performance baseline,” which will be used to analyze potential performance improvements.
* Define goals and requirements for the site. For example, “The front page must load in under two seconds for anonymous traffic,” and “A site search must not take more than three seconds on average to return results.”
* Actually perform your review. This often involves running a load test, reviewing configuration files, profiling pages, and reviewing slow query logs.
* Define a list of potential improvements based on the site goals and requirements, using the information gathered in the performance baseline and your review.
* What is the cost of the improvement, both in terms of staff time and any hardware or software purchases that may be necessary for the change?
* Once an improvement has been made, what impact does it have?

### 2 - Frontend Performance

* The 14 rules defined by Steve Souders’s High Performance Websites (O’Reilly) remain a good reference point for examining the pages served by a site and identifying areas for improvement (see this <a href="http://stevesouders.com/hpws/rules.php">page</a> for a refresher).
* Google’s <a href="https://developers.google.com/speed/pagespeed/">PageSpeed</a> and Yahoo!’s <a href="https://developer.yahoo.com/yslow/">YSlow</a> will quickly grade a single page of your site and identify the highest-priority areas<br />
for improvement.
* Limiting HTTP Requests:Use Drupal's CSS and JavaScript aggregation via a configuration option. This allows potentially dozens of individual requests for CSS and JavaScript files to be reduced to just a few.
* Minification: Drupal's core support for CSS/JS minification is limited. 3 different options are discussed.
* Image Requests: You can optimize images for bandwidth using image derivatives, which ensure the images are scaled or cropped to the size they will be served at.
* Compression: Serving files with gzip compression and respecting Accept headers allows file size to be reduced drastically.
* Cacheable Headers: assets are set to have an Expires header of two weeks via .htaccess if mod_expires is enabled in Apache. For sites that aren’t undergoing frequent releases, you may want to tweak this upward.
* Load third party javascripts asynchronously. Ensure you audit sites for SPOFs; SPOF-O-Matic browser plugin-in is great for this.

### 3 - Drupal Performance Out of the Box

* Internal Page Caching: Drupal core provides its own internal page cache. The configuration option is accessed via admin/config/development/performance and allows the full rendered HTML output to be stored using Drupal’s own cache API.
* The configuration settings $conf['page_cache_invoke_hooks'] = TRUE and $conf['page _cache_without_database'] = TRUE allow Drupal 7 to skip even more of its usual bootstrap when serving cached pages, so that pages may be served without any database or cache lookups except for the page cache item itself.
* Reverse Proxy Caching: The “Expiration of cached pages” option is located at admin/config/development/performance. Setting this option affects the max_age value of the Cache-Control header sent by Drupal, which allows reverse proxies to cache pages.
* The most common reverse proxy used for Drupal sites is Varnish. Using a reverse proxy such as Varnish to serve cached pages has&nbsp; advantages over the internal page cache, since Varnish is able to serve the entire page request without having to call back to Apache and PHP.
* Logging: Verbose logging and PHP errors should be fixed at source, by auditing the logs periodically and fixing custom code or submitting patches to contributed code to avoid the logging or errors.
* Cache: Drupal’s cache API uses the database storage implementation by default. Contributed projects are available providing support for Memcache, Redis, MongoDB, APC, and Files.
* Cron: Cron has high resource/memory requirements, so it should be run via drush to avoid taking up a web server process and&nbsp; artificially inflating PHP memory limit requirements with mod_php.
* Views: Views (both the Drupal 8.x core version and the Drupal 7 contributed module) ships with a built-in time-based caching system, while additional modules can also provide alternative caching implementations.
* Views: If you’re concerned about cache coherency, setting a longer value for query caching and a shorter value for rendered output is a good compromise.

### 4 - Drupal Coding for Optimal Performance

* Much of the work done on a Drupal site involves getting a list of entities and then rendering them. There are two APIs introduced in Drupal 7, and only slightly changed in Drupal 8, that help with this: entityQuery() and entity_load_multiple().
* entityQuery: Rather than a direct database query to entity and field tables, EntityQuery() relies on a storage controller to handle building and executing the query for the appropriate entity storage backend
* entity_load_multiple(): Drupal 7 introduced multiple entity loading and rendering so that tasks such as fetching field values from the database could be done once for all nodes with an IN() query rather than executed individually:
* Caching is often the quickest way to solve a performance issue. By adding caching in a particular code path, you can ensure that it will only be executed on cache misses.
* Static caching: When code is run multiple times per request, a common optimization is to add a static cache around it.
* Persistent caching: While static caching allows code to skip execution when called within a single PHP request, persistent caching is shared between PHP processes and can retain data for anything from a few seconds to several weeks.
* Drupal core ships with a rich caching API, defaulting to database caching but with contributed support for files, Memcache, Redis, MongoDB, APC, and other backends.
* Queues and Workers: The queue API is most useful when you have expensive operations triggered by actions on the site. Instead of doing all work inline in your hook implementations, you can create a queue item; then, in the worker callback, you can perform whichever tasks on it are necessary. Drupal core ships with a robust queue API, defaulting to MySQL but with contributed projects providing support for Redis, Beanstalkd, and others.

### 5 - Analyzing Frontend Performance

* Often the first step toward analyzing a site’s frontend performance is looking at the summary report produced by a tool such as <a href="http://yslow.org/">YSlow </a>and <a href="https://developers.google.com/speed/pagespeed/">PageSpeed</a>. However, the reports they generate are only useful if you have an understanding of the underlying principles of frontend performance and the effort it’s likely to take to fix the issues that are identified.
* Waterfall charts show the sequential process of loading a web page. Primarily this shows page resources as they’re requested and downloaded by the browser, as well as timings for “start render” and “document complete” events, represented by the green and blue vertical bars on the chart.
* Real User Monitoring (RUM) tends to be dominated by commercial Software as a Service (SaaS) solutions, since it requires capturing, storing, and presenting large volumes of information that many organizations do not have the ability to maintain in-house. However, there are open source alternatives, such as Jiffy (open sourced by Netflix) and Steve Souders’ Web Episodes, and free commercial alternatives such as the <a href="https://support.google.com/analytics/answer/1205784?hl=en&amp;topic=1120718&amp;utm_source=gablog&amp;utm_medium=blog&amp;utm_campaign=newga-blog&amp;utm_content=sitespeed">page timing feature of Google Analytics</a>.

<img alt="" src="/img/notes/drupal-performance-waterfall.png" style="width: 499px; height: 409px;" />

### 6 - Analyzing Application Performance

* The <a href="http://drupal.org/project/devel">Devel</a> module provides a number of debugging tools for developers. Amongst these are tools for monitoring page timing, memory consumption, and the query log, and for XHProf profiling.
* Profiling using XDebug. Xdebug profiling generates cachegrind files, which can then be viewed in an interface such as KCacheGrind or or webgrind, which is written in PHP and has a web interface
* Profiling using XHProf, developed and open sourced by Facebook. As a profiler, XHProf has several advantages over Xdebug and is our preferred profiling tool for Drupal.
* Sometimes it’s necessary to go to the system level to understand what’s happening with your application. strace is a great tool for looking at what happens here.

<img alt="" src="/img/notes/drupal-performance-devel-query-log.png" style="width: 565px; height: 491px;" />

### 7 - Infrastructure Design and Planning

* Horizontal scaling involves adding additional servers while vertical scaling involves adding more resources to existing servers. A good infrastructure plan will take both of these scaling approaches into account, as there are times when one may be more appropriate than the other.
* <strong>Service Categorisation:</strong><br />
<em>Web:</em> Apache, Nginx, or another web server of your choice, plus PHP<br />
<em>Database:</em> Most commonly MySQL, though Drupal core supports many other databases to a lesser degree, including PostgreSQL, Microsoft SQL Server, Oracle, and SQLite<br />
<em>Frontend proxies:</em> Varnish, CDN, Nginx, or other proxy caches<br />
<em>Other caches and data stores:</em> Memcache, Redis, MongoDB, etc.<br />
<em>Search backends:</em> Apache Solr, Elasticsearch, Sphinx, etc.<br />
<em>Load balancers:</em> HAProxy, Nginx, IPVS, etc.
* <strong>Example Two-Layer Configuration</strong><br />
<img alt="" src="/img/notes/drupal-performance-two-server.png" style="width: 558px; height: 209px;" />
* <strong>Example Larger-Scale Infrastructure</strong><br />
<img alt="" src="/img/notes/drupal-performance-drupal-multilayer.png" style="width: 558px; height: 313px;" />
* <strong>Internal Network Layout</strong><br />
<img alt="" src="/img/notes/drupal-performance-network-layout.png" style="width: 552px; height: 209px;" />

### 9 - File Storage for Multiple Web Servers

* rsync: The first option we should consider is, at first glance, the simplest. In this configuration, you would simply set up rsync processes between your web nodes to ensure that every file on webnode1 is also on webnode2, webnode3, and so on.
* Gluster is one of the most popular options for file storage, and in particular file storage in cloud deployments. It is popular for good reason — Gluster is simple to set up, does not create a single point of failure, and works extremely well for many types of deployments.<br />
<img alt="" src="/img/notes/drupal-performance-glusterfs.png" style="width: 555px; height: 173px;" />
* Single NFS Server: A decreasingly common practice is to set up an NFS share somewhere on your cluster and just hope for the best.
* HA NFS Cluster: Use DRBD (a kernel-level distributed remote block device) to replicate the actual block device NFS is hosted on. This allows you (with some significant configuration) to successfully fail over an NFS mount without having stale file handles on the client side (refer to the book for more details on the configuration).

### 10 - MySQL Optimization

* MySQL configuration can generally be split into global configuration, per-thread configuration, and storage engine configuration.
* MySQL Global Configuration key parameters: Drupal doesn’t require tuning of many nonstandard global options. Those you may<br />
want to consider include: max_connections, key_buffer and query_cache_size (refer book for more details)
* Per-Thread Configuration key parameters: Many of the quality configuration templates, such as those from the<a href="https://iuscommunity.org/pages/About.html"> IUS RHEL/CentOS repositories</a> or <a href="https://tools.percona.com/wizard">Percona Tools for MySQL</a> have good defaults for these values. The only per-thread settings we often recommend tweaking for a Drupal website are those affecting temporary tables: tmp_table_size/max_heap_table_size
* Storage Engine Configuration: Unlike most of the per-thread configuration, correctly configuring InnoDB is extremely important. In particular, the InnoDB buffer size and transaction flush method are possibly the most important tunables for a Drupal installation.
* Replication: Most every production deployment will need a replicated slave, at the very least for failover during crashes and/or updates. Drupal-specific considerations: Replication type, Slave lag and Read-only queries
* The <a href="http://www.percona.com/software/percona-toolkit">Percona Toolkit</a> is a must-have toolkit and contains over 30 tools, varying from hugely complicated and impactful utilities to glorified output reformatters. Some of the most important tools include: pt-query-digest, pt-table-checksum, pt-online-schema-change, pt-index-usage
* While Percona Toolkit tends to be slightly more focused on performance optimization and replication, <a href="http://code.openark.org/forge/openark-kit">openark kit</a> is more focused on operational tools. Few of the tools we have found most useful:oak-chunk-update, oak-kill-slow-queries, oak-purge-master-logs, oak-security-audit, oak-show-limits
* <a href="http://blog.codenode.com/2015/01/hack-mysql-has-been-retired.html">mysqlreport</a> has been the standard in “general MySQL statistics” for as long as we can remember. It should be run on every MySQL server at least once. This utility will print out current usage information for the query cache, key buffer, and InnoDB buffer pool, as well as detailed InnoDB statistics and much more.
* <a href="http://www.percona.com/software/percona-monitoring-plugins">Percona Monitoring Plug-Ins</a> is a set of extensions for Nagios/Icinga and Cacti that make them far more useful for MySQL administrators.
* Query Optimization: The chapter covers some very basic optimization, index usage, and join optimization techniques.
* Query Optimization: While tuning is important, it often has nowhere near the impact of actually fixing a poorly performing query... It is highly recommended that you get a book on this subject, as it is a very deep one and is well worth learning for any Drupal developer.

The book also covers the following topics in depth:

* Failover Configuration
* Alternative Storage and Cache Backends (Memcache in Depth)
* PHP and httpd configuration for high performance
* Reverse proxies (Varnish) and Content Delivery Networks
* Load Testing using JMeter

