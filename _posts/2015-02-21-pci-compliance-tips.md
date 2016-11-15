---
layout: post
title: PCI Compliance Tips
---

The Payment Card Industry (PCI) Data Security Standard (DSS) is an information security standard defined by the Payment Card Industry Security Standards Council. The certification is designed to prevent credit card fraud through increased controls around data and its exposure to compromise.

PCI compliance requires applying many configuration changes to your servers: hardening settings, centralized logging, anti-virus, web application firewall etc. Here are some useful open source tools that can help system administrators prepare their infrastructure for PCI compliance.

### Puppet

Puppet helps you to develop infrastructure as code modules there by maintaining consistent settings across servers and reducing human errors. It is generally acceptable to show the Puppet modules to the auditor to demonstrate what settings are applied to the PCI servers.

Puppet also let’s you create “environments”. You can classify nodes into a pci environment and apply all PCI modules to the nodes within the PCI environment.

For more info: <a href="http://puppetlabs.com/puppet/puppet-open-source">http://puppetlabs.com/puppet/puppet-open-source</a>.

### Hardening Servers

It is a PCI requirement to harden the servers with industry-accepted system hardening standards. The following guides will be useful:

<a href="http://benchmarks.cisecurity.org/tools2/linux/CIS_Debian_Benchmark_v1.0.pdf">http://benchmarks.cisecurity.org/tools2/linux/CIS_Debian_Benchmark_v1.0.pdf</a>

<a href="http://benchmarks.cisecurity.org/tools2/linux/CIS_Debian_Benchmark_v1.0.pdf">http://www.sans.org/score/checklists/linuxchecklist.pdf</a>

### Splunk / Logstash

PCI requires a good system to monitor logs. Splunk and Logstash are two of the best open source options available. While Splunk is the best centralised logging software, the free edition is for individual use and supports upto 500 MB/day. Logstash is a good alternative and completely free.

You can forward the syslog and auth.log to the centralized logging server where you can generate and export many reports for requirement 10 in particular.

If Splunk is used to index logs from non-PCI servers as well, you can have a separate index for PCI logs and using user roles in Splunk, only allow PCI users to view PCI logs.

For more info: <a href="http://www.splunk.com/view/pci-compliance/SP-CAAACPS">http://www.splunk.com/view/pci-compliance/SP-CAAACPS</a>

### OSSEC

PCI requires that Intrusion detection must be on every server, file integrity monitoring must be enabled and alerts must be monitored and responded to. OSSEC is a popular open source Host Intrusion Detection System and a File Integrity Monitor. It integrates well with Splunk by using the Splunk for Ossec app which can create some nice pie charts, generate reports for the different alerts etc. You can also configure Splunk to only email an alert for specific alert levels.

For more info: <a href="http://www.ossec.net/files/ossec-PCI-Solution-2.0.pdf">http://www.ossec.net/files/ossec-PCI-Solution-2.0.pdf</a>

### Auditd

In addition to the logs being sent to Splunk/Logstash for indexing, the integrity of logs need to be maintained on the servers. Auditd can provide detailed audit trails for anything on the system. Auditd can be configured to audit all files under /etc and executable paths such as /bin, /usr/bin etc. A good start is to add the syslog and auth.log to auditd to ensure they are not being tampered.

For more info: <a href="http://linux.die.net/man/8/auditd">http://linux.die.net/man/8/auditd</a>

### Mod Security

PCI compliance requires the web servers to have a web application firewall installed. Mod Security is a web application firewall and can run on Apache, Nginx, and IIS web servers.

It is mandatory that the latest version of the Core Rules Set is also used with Mod Security. You may have to compile Mod Security yourself and integrate the CRS manually.

Mod Security will log into the error log and can be sent to Splunk for indexing.

### AWS CLI Tools

PCI DSS typically requires auditable bidirectional firewalls.&nbsp;Networks must also be properly segmented.

AWS allows you to setup Virtual Private Cloud (VPC) where components are segregated into public and private subnets, the former acting as DMZ zones. VPCs allow you to create private subnets in the ip range of your choice and use NAT to isolate your instances from internet.

Segmentation can exist between instances inside the same subnet based on roles (services). Along with ingress and egress rules for security groups, network ACLs are also controlled for critical components.

In order to describe how the Security Groups, Access Control Lists, Subnets, Route Tables etc are designed within a VPC, we can dump all the settings using the aws cli tool. We can then take an example instance and match it with it’s Security Groups, Subnets and ACL(s).

PCI also requires that changes to the firewall (Security Groups) are logged. Using the ec2 api tools, you can write an audit script to log all the changes.
