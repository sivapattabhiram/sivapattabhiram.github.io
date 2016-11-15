---
layout: post
title: Open Cloud Reference Architectures
---

Rackspace has published a set of useful <a href="http://www.rackspace.com/knowledge_center/article/rackspace-open-cloud-reference-architecture">cloud reference architectures</a> for Content Management System (Drupal), Blog (Wordpress) and a E-Commerce portal. While it lacks in specific implementation details, it still gives a good overall summary of the key building blocks of cloud architectures.

### Content Management System Architecture

<img alt="" src="/img/notes/rackspace-cms-arch.png" style="width: 700px; height: 385px;" />

* Application configured to heavily utilize caching, both with Varnish for some static content and Memcached for database query caching.
* This configuration also allows you to scale horizontally quickly for events or high traffic.
* Cloud Monitoring watches the infrastructure to make sure everything is working efficiently and properly.
* Cloud Load Balancers have multiple balancing options, and support SSL termination for secure sites.
* Cloud Servers utilizing Cloud Block Storage serve web content for this application.
* Cloud Databases will host MySQL in a optimized, redundant platform.
* Static content can be served through Rackspace Cloud Files and the Akamai CDN for global content delivery.

### Wordpress Cloud Architecture

<img alt="" src="/img/notes/rackspace-wordpress-arch.png" style="width: 700px; height: 399px;" />

* W3 Total Cache plugin used for integrating Wordpress with CDN
* Cloud Servers utilizing Cloud Block Storage serve web content for this application.
* Cloud Databases will host MySQL in a optimized, redundant platform.
* Memcached is used for MySQL query and/or session data.

### E-Commerce Portal

<img alt="" src="/img/notes/rackspace-ecommerce-arch.png" style="width: 700px; height: 421px;" />

* Shows the power of Rackspace Cloud Tools Marketplace - customers can sign up for partner services like SendGrid for end user email deliverability.
* This configuration also takes advantage of a bare metal database server for PCI compliance standards and to take full advantage of computing resources.
* When a visitor loads a webpage, Rackspace Cloud Servers are used as web and application servers. A Firewall and Load Balancer are utilized here.
* The Rackspace Cloud Servers can be segmented from each other using Rackspace Cloud Networks.
* Using RackConnect, cloud resources can be connected seamlessly to dedicated hardware, in this case for use as a database server.
* Customer email can be sent through Mailgun, Rackspace subsidiary. Customers can also use a payment gateway to meet PCI requirments.