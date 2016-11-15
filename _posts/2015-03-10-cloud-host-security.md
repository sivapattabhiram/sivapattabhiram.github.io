---
layout: post
title: Cloud Host Security
---

Host Security describes how your server is setup for for the following tasks:

* Preventing the attacks
* Detecting and minimizing the impact of attacks on the overall system
* Responding to attacks when they occur.

<a href="http://shop.oreilly.com/product/9780596156374.do">Cloud Application Architectures</a> by O'Reilly is an excellent introduction to building applications and infrastructures in the Cloud. The chapter on host security outlines some of the best practices to ensure that your cloud systems are secure. Here is a quick summary:

### Minimize the number of services

Each service you run on a host presents a distinct attack vector into the host. The more services, the more likely an attacker will find one with a security exploit. You must therefore minimize the different kinds of software running on a server.

### Rapid Rollout of security patches

Your most significant tool in preventing attackers from exploiting a vulnerability once it becomes known is the rapid rollout of security patches. In the cloud, rolling out a patch across the infrastructure takes three simple steps:

* Patch your AMI with the new security fixes.
* Test the results.
* Relaunch your virtual servers.

Here a tool such as Puppet or Chef for managing your infrastructure becomes absolutely critical. If you have to manually perform these three steps, the cloud can become a horrible maintenance headache. Management tools, however, can automatically roll out the security fixes and minimize human involvement, downtime, and the potential for human-error-induced downtime.

### System Hardening

You should always harden the system before creating your image. Server hardening is the process of disabling or removing unnecessary services and eliminating unimportant user accounts. Tools such as Bastille Linux can make the process of hardening your machine images much more efficient. Once you install Bastille Linux, you execute the interactive scripts that ask you questions about your server. It then proceeds to disable services and accounts. In particular, it makes sure that your hardened system meets the following criteria:

* No network services are running except those necessary to support the server’s function.
* No user accounts are enabled on the server except those necessary to support the services running on the server or to provide access for users who need it.
* All configuration files for common server software are configured to the most secure settings.
* All necessary services run under a non privileged role user account (e.g., run MySQL as the mysql user, not root).
* When possible, run services in a restricted filesystem, such as a chroot jail.
* Before bundling your machine image, you should remove all interactive user accounts and passwords stored in configuration files.

### Antivirus Protection

Some regulations and standards require the implementation of an antivirus (AV) system on your servers. AV systems are definitely necessary in some circumstances, but a risk in others. For example, if you are accepting the upload of photos or other files that could be used to deliver viruses that are then served to the public, you need to use some kind of antivirus software in order to protect your site from becoming a mechanism for spreading the virus. But an AV system with an exploit is itself an attack vector and, on some operating systems, the percentage of AV exploits to known viruses is relatively high. Once you have selected an AV vendor and implemented it on your servers, you absolutely must keep your signatures up to date.

### Host Intrusion Detection

A host intrusion detection system (HIDS) such as OSSEC monitors the state of your server for anything unusual. An HIDS examines the system for all signs of compromise and notifies you when any core operating system or service file changes. OSSEC, a popular HIDS has two configuration profiles:

* Standalone, in which each server scans itself and sends you alerts.
* Centralized, in which you create a centralized HIDS server to which each of the other servers sends reports.

In the cloud, you should always opt for the centralized configuration. It centralizes your rules and analysis so that it is much easier to keep your HIDS infrastructure up to date.

The following figure illustrates a cloud network using centralized HIDS.

<img alt="" src="/img/notes/host-security-ossec-arch.png" style="width: 469px; height: 466px;" />

As with an AV solution, you must keep your HIDS servers up to date constantly, but you do not need to update your individual servers as often. The downside of an HIDS is that it requires CPU power to operate, and thus can eat up resources on your server. By going with a centralized deployment model, however, you can push a lot of that processing onto a specialized intrusion detection server.

### Data Segmentation

Data segmentation helps you to minimize the impact of the compromise of individual nodes in a cloud network. The advantage of Data segmentation are:

* Access to your most sensitive data requires a full system breach.
* The compromise of the entire system requires multiple attack vectors with potentially different skill sets.
* The downtime associated with the compromise of an individual node is negligible or nonexistent.

For example, we can separate credit card data from customer data and store them at different places. An attacker who accesses your customer database still lacks access to the credit card data. To be able to access credit card data, decrypt it, and associate it with a specific individual, the attacker must compromise both the e-commerce application server and the credit card processor. Here again the approach of one server/one service helps out. Because each type of server in the chain offers a different attack vector, an attacker will need to exploit multiple attack vectors to compromise the system as a whole.

### Credential Management

Your machine images OSSEC profile should have no user accounts embedded in them and you should never allow password-based shell access to your virtual servers. The most secure approach to providing access to virtual servers is the dynamic delivery of public SSH keys to target servers. If someone needs access to a server, you should provide her credentials to the server when it starts up instead of embedding that information in the machine image.

Although, it is perfectly secure to embed public SSH keys in a machine image, if you embed the public key credentials in a machine image, the user behind those credentials will have access to every machine built on that image. To remove her access or add access for another individual, you subsequently have to build a new machine image reflecting the changed dynamics.

Therefore, you should keep things simple and maintainable by passing in user credentials as part of the process of launching your virtual server. At boot time, the virtual server has access to all of the parameters you pass in and can thus set up user accounts for each user you specify. It’s simple because it requires no tools other than those that Amazon already provides.