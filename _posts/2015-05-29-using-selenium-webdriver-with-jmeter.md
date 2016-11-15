---
layout: post
title: Using Selenium WebDriver with JMeter
---

<img alt="" src="/img/notes/jmeter-selenium.png" style="width: 295px; height: 99px;" />

JMeter supports execution of Selenium scripts through the JMeter WebDriver plugin which automates the execution and collection of Performance metrics on the Browser (client-side). Normally JMeter is used for performance testing of the server side components. The JMeter HTTP Sampler sends an HTTP get request to the server and the output is analyzed as plain text. The average response time reported by JMeter tests is not same as the browser execution time. In order to measure the real browser execution time the following are essential.

* Client-side Javascript execution - eg. AJAX, JS templates
* CSS transforms - eg. 3D matrix transforms, animations
* 3rd party plugins - eg. Facebook like, Double click ads, site analytics, etc

All these things add to the overall browser execution time, and this project aims to measure the time it takes to complete rendering all this content.

Blazemeter has provided a detailed step by step instructions on integrating Selenium WebDriver with JMeter [here](http://jmeter-plugins.org/wiki/WebDriverSampler/). 

Few additional pointers that could be of help:

a) You need to have the right version of Firefox for the WebDriver Sampler to work with Firefox. As per the documentation here, this is the recommended combination:

<table border="1" cellpadding="3" cellspacing="0" class="bordered" style="font-size: 13px; color: #000000; font-family: Tahoma, Arial, Verdana, sans-serif; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: auto; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: auto; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: #ffffff;">
	<tbody>
		<tr>
			<td style="font-size: 1em;">JMeterPlugins version</td>
			<td style="font-size: 1em;">Firefox compatible version</td>
		</tr>
		<tr>
			<td style="font-size: 1em;">1.1.2</td>
			<td style="font-size: 1em;">22</td>
		</tr>
		<tr>
			<td style="font-size: 1em;">1.1.3</td>
			<td style="font-size: 1em;">26</td>
		</tr>
	</tbody>
</table>

b) By default Firefox automatically installs updates for you. You need to disable this option.

<img alt="" src="/img/notes/jmeter-firefox-options.png" style="width: 533px; height: 385px;" />

In my case, I am using JMeter WebDriver 1.1.3 with Firefox 26.

c) The Thread Group properties should all be 1.

<img alt="" src="/img/notes/jmeter-threadproperties.png" style="width: 600px; height: 144px;" />

d) Blazemeter recommends that the number of WebDriver samplers should be less than the number of JMeter samplers. If you need to get any values from websites through Ajax you can use WebDriver with the Once Only Controller in order to avoid continual/duplicate browser launches.

e) Do not use the web driver to create large scale load. The selenium web driver runs a physical browser on the load server machine which consumes a significant amount of resources. Hence use standard HTTP samplers to create the majority of the load and use web driver to get real browser timings.

