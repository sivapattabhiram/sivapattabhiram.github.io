---
layout: post
title: Nagios for Cloud Monitoring
---

While Nagios is the defacto standard in IT infrastructure monitoring, its usage in monitoring cloud infrastructure is not well known. Here are two good case studies that puts Nagios as one of the favorite choices in cloud infrastructure monitoring:

### Internal Monitoring at Acquia

<a href="https://docs.acquia.com/cloud/arch/monitor">https://docs.acquia.com/cloud/arch/monitor</a>

Acquia uses the Nagios monitoring platform to provide instant access to vital, real-time, and historical metrics of the servers it provisions in the AWS infrastructure, which includes the sites that Acquia manages for its customers. Acquia monitors over 40 metrics, which are linked to alerts via email, SMS, and pager. Metrics include, but are not limited to, the following: Disk usage, CPU usage, Memory usage and swap activity, Running processes, Database size and available storage space, Many Drupal-specific metrics. Acquia's monitoring systems use alerting and escalation to ensure that our operations team is notified when alerts are generated.

### Infrastructure Monitoring at Groupon

<a href="http://newrelic.com/case-studies/groupon">http://newrelic.com/case-studies/groupon</a>

As per this case study, to help the team keep an eye on the health and availability of Groupon.com, Groupon has implemented several tools including Nagios for infrastructure monitoring, Pingdom for availability monitoring, and New Relic RPM for 24×7 application monitoring.

As per the survey done by <a href="http://blog.dataloop.io/2014/01/30/what-we-learnt-talking-to-60-companies-about-monitoring/">Dataloop</a> by talking to over 60 companiies about monitoring, 50% of them use Nagios (<a href="http://dataloopio.files.wordpress.com/2014/01/monitoring_tools_deployed1.png">Graph 1</a>) and usage of Nagios starts once the number of servers crosses 20 (<a href="http://dataloopio.files.wordpress.com/2014/02/monitoring_tools_deployed_servers.png">Graph 2</a>).<br />
Two interesting conclusions from this study:

1. Smaller companies (&lt; 20 servers) tend not to have any DevOps/Operations people in their company, so developers tend to use simpler to install SaaS monitoring tools, or tools that help them such as Graphite or LogStash. When the company has the resources to bring in a DevOps/Operations person or team and they start bringing in the infrastructure monitoring tools like Nagios.

2. In some of the larger services it wasn’t uncommon to find 4-5 monitoring solutions in place. For example Nagios for alerting, Graphite for dashboards, StatsD for developer metrics, collectD for service metrics and LogStash/Kibana for logs.

