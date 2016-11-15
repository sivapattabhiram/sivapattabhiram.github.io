---
layout: post
title: Caching Static Resources - Best Practices
---

Caching is one of the effective ways to improve the performance of your website. Typically, 87% of your web site consists of static and re-usable content which can be cached.

Caching of static resources like css, js and image files can be done at different tiers.

* Browser Cache
* Edge Cache
* Origin Cache

In this post, we will look into each of the caching tier in detail, the best practices for caching static resources and also how as a site owner/tester you can ensure that all your static resources are cached at the appropriate places for the specified time.

Here is a reference architecture on Amazon AWS utilizing cache at multiple layers:

<img alt="" src="/img/notes/caching-static-resources.png" style="width: 720px; height: 540px;" />

### Browser Cache

Browser cache helps to eliminate the network latency. This is achieved by setting the max-age or expiry date in the headers using modules like mod_expire (apache). By the webserver telling the browser to store these files and not download them when you come back saves your users time and your web server bandwidth.

The cache size is limited though (IE is 8-50MB, Chrome is &lt; 80MB, Firefox is 50MB etc).

### Edge Cache

Edge caches (CDN) work in a manner similar to the browser caches. When a request comes into an edge server, it first checks the cache to see if the content is present. The cache key is the entire URL including query string (just like in a browser). If the content is in cache and the cache entry hasn’t expired, then the content is served directly from the edge server.

If, on the other hand, the content is not in the cache or the cache entry has expired, then the edge server makes a request to the origin server to retrieve the information. When the edge server receives the response from the origin server, it stores the content in cache based on the HTTP headers of the response.

When the browser makes a DNS request for a domain name that is handled by a CDN, the DNS server does a geographic lookup based on the DNS resolver’s IP address and then returns an IP address for an edge server that is physically closest to that area.

[Amazon Cloudfront](http://aws.amazon.com/cloudfront), [Akamai](http://www.akamai.com) are some of the popular CDN solutions.

### Origin Cache

Origin cache or proxy caches live between your CDN/Users and your web tier. They can offer increased performance by reducing the application and database load.

They work similar to the edge caches - When a request comes, it first checks the cache to see if the content is present. If the content is in cache and the cache entry hasn’t expired, then the content is served from the web cache. If not, the cache server makes a request to the application server, caches the content and serves it to the user.

[Varnish](<https://www.varnish-cache.org), [Nginx](http://www.nginx.org), and [Squid](http://www.squid-cache.org/) are some of the popular web cache solutions.

### Best Practices

* For statics resources like css, js and image files, set the "Cache control: max-age" header directive to a high value. <a href="https://developers.google.com/speed/docs/insights/LeverageBrowserCaching">Google</a> recommends a minimum cache time of one week and preferably up to one year for static assets, or assets that change infrequently.
* Minify and aggregate css/js files. Generate uniques names for all aggregates using a hash value of the content. This ensures that whenever any change to css or js files happen, an aggregate file with a new name gets generated and which is fetched by the browser on page refresh.
* CDNs like Amazon Cloudfront uses "Cache-control: maxage" to determine the cache lifetime. If you want to set the cache lifetime in Cloudfront to be different from that of the browser, you can use the "Cache-Control s-maxage" directive.
* In the origin cache configuration, set a high ttl value for static resources. For example, in Varnish proxy, this can be achieved by setting the beresp.ttl value inside vcl_fetch. In my experience, a ttl value of 24 h works fine. This means that Varnish will cache all static resources for 24 h after which it will query the web server for the content again.
* If you modify any image, ensure that it is saved in a new name. With the above configuration, images will be cached at multiple cache layers. While the origin cache and edge cache can be invalidated, we dont have any control over the user's browser. Hence saving an image with a new name always ensures that the browser refresh fetches the new image.
* Fine tune the size of the cache for the origin server. You can start with an initial size depending on your system available memory and adjust it by monitoring the cache hit ratio of the origin server regularly. For Varnish, the n_lru_nuked is a good indicator - it increases each time Varnish throws something out of the cache because it is running low on storage.

### Testing

As a site owner or developer, how do you ensure that all your static resources are cached at the appropriate places for the specified time? The simplest way is to use Google Chrome with Developer Tools window enabled (Ctrl+Shift+I). Navigate to the Network tab in Developer Tools and now access the page that you wanted to test.

Click on a static resource like js, css or image file inside the Developers Tools Network Tab and check the following parameters:

* The Cache-Control directive in the Response header should be set with a high max-age value
* Look for CDN directives. If you are using Amazon Cloudfront then you will see X-Cache directive which is set either to "Hit from Cloudfront" or "Miss from Cloudfront"
* For Origin Caches, we may have to add a custom header for debug purposes. In my case, I have added a X-Varnish-Cache header directive with "HIT" or "MISS" value.

Now you can check whether Browser Cache, Edge Cache and Origin Cache is working as intended:

* Testing Browser Cache: Load a page and click on a static resource in the Developer Tools Network window. Check that the Cache-Control directive in response header has high max-age value. Now refresh the page (dont reload) by clicking on the url bar and pressing enter key. Now check the HTTP status code for this resource. It should either to be '200 OK (from cache)' or '304 Not Modified (from cache)'. This ensures that the browser is caching the resources and loading it from the cache.
* Testing Edge Cache: Load a page few times from the browser. Now click on the static resource in Developer Tools Network Tab and check the header response. The X-Cache directive in the response header should be "Hit from Cloudfront". Since Cloudfront can serve the request from multiple edge locations, it could take some time for cache to propagate. But once you get a "Hit from Cloudfront" it normally stays that way.
* Testing Origin Cache: This is tricky when you are accessing it over CDN. Since CDN also caches the page, you may be seeing the cached version from CDN than from the origin cache. For testing, access the resource directly using the serve IP and check the X-Varnish-Cache header value. If the resource is accessed the first time, it will be a "MISS". Subsequent access will show "HIT" value.
* You can also access the page from [http://webpagetest.org](http://www.webpagetest.org/) and test that the CDN caching is working from multiple locations.
