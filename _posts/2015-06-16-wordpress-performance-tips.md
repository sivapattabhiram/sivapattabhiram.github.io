---
layout: post
title: Wordpress Performance Tips
---

<a href="https://rtcamp.com/wordpress-nginx/tutorials/">rtcamp's wordpress-nginx tutorials</a> are an excellent reference for those looking to improve the performance of their Wordpress sites. They have a good set of recommendations at different levels of the stack - web server, php, and mysql.

Here are some of the key recommendations along with links to their tutorials.

### Web Server


* Use Nginx as your web-server. Tutorial on installing PHP 5.5, MySQL, Postfix, Nginx and WordPress on Ubuntu <a href="https://rtcamp.com/tutorials/linux/ubuntu-php-mysql-nginx-postfix/">here</a>.
* Use Nginx <a href="https://rtcamp.com/wordpress-nginx/tutorials/single-site/fastcgi-cache-with-purging/">fastcgi_cache</a> for page cache. You will need <a href="http://wordpress.org/extend/plugins/nginx-helper/">nginx-helper</a> if you are using fastcgi_cache.
* <a href="https://rtcamp.com/tutorials/cdn/">Use CDN for static assets</a> e.g. images, css and JS files.
* Combine CSS and JS files and if possible minify them. Try doing this via pagespeed. If that is not possible, use W3 Total Cache for this. Be very careful with this step as it can break your themes and plugins.
* Optimize image sizes with lossless image compression. You can do this either using pagespeed or a WordPress plugin like <a href="https://wordpress.org/plugins/wp-smushit/">smush.it</a>.
* <a href="https://rtcamp.com/tutorials/nginx/block-wp-login-php-bruteforce-attack/">Protect wp-login.php from bruteforce attack at nginx (web-server) level</a>. A strong password can save you from getting hacked, but bruteforce login attacks create unnecessary PHP-MySQL load.
* If you are using SSL, use it for complete site. Avoid using SSL for parts of your site. <a href="https://rtcamp.com/tutorials/nginx/ssl-pci-compliance-performance/">SSL can be tweaked as well.</a>


### PHP

* We recommend using nginx with <a href="https://rtcamp.com/tutorials/php/hhvm-with-fpm-fallback/">HHVM with PHP-FPM fallback</a>.
* If HHVM doesn’t work with your plugins and themes, then Nginx with PHP-FPM is best.
* When it comes to PHP, always use latest PHP version, even though WordPress supports outdated PHP versions (which exposes WordPress sites to security risks)
* Always use <a href="https://rtcamp.com/tutorials/php/zend-opcache/">Zend Opcache</a> for opcode caching. PHP 5.5. has zend opcache support in core. Since APC is not updated from long time, we made a switch to new Zend Opcache.
* Move session <a href="https://rtcamp.com/tutorials/php/session-tmpfs/">storage to tmpfs</a> or <a href="https://rtcamp.com/tutorials/php/memcache/">memcache</a>
* Use memcache for object-cache. You can do it using <a href="http://rtcamp.com/wordpress-nginx/tutorials/single-site/w3-total-cache/">W3 Total Cache plugin</a>.
* <a href="https://rtcamp.com/tutorials/php/xdebug-webgrind/">Analyze PHP code performance using xdebug and webgrind.</a>


### MySQL

* Use latest MySQL version. Though WordPress will work with older version, new mysql versions has better performance.
* Periodically tweak mysql configuration, using scripts like <a href="https://rtcamp.com/tutorials/mysql/tuning-primer/">tuning-primer</a> and <a href="https://rtcamp.com/tutorials/mysql/mysqltuner/">mysqltuner</a>
* Enable <a href="https://rtcamp.com/tutorials/mysql/slow-query-log/">slow query log</a> and analyze it with <a href="https://rtcamp.com/tutorials/mysql/slow-query-log-anemometer/">anemometer</a>. This is highly recommended if you are seeing site slowed down for non-logged in users.
* <a href="https://rtcamp.com/tutorials/mysql/query-profiling/">Profile mysql query</a> to check internal execution.
* <a href="https://rtcamp.com/tutorials/mysql/myisam-to-innodb/">Convert MyISAM tables to InnoDB tables</a>. If you have plugins which relies on custom post type, this is must.
* Use <a href="https://rtcamp.com/tutorials/mysql/tmpfs-temporary-folder-creation/">tmpfs for mysql temporary tables</a>.
* Add mysql indexes if possible to speed up queries. Many plugin developers don’t take efforts to add proper mysql indexes for MySQL tables there plugin creates.

### Tools

* You can use <a href="http://gtmetrix.com/">gtmetrix.com</a> and/or <a href="http://www.webpagetest.org/">webpagetest.org</a>. Try to score as many “A” as possible.

rtcamp also has developed <a href="https://rtcamp.com/easyengine">EasyEngine</a> tool which does most of the above stuff for you. Do check it out.
