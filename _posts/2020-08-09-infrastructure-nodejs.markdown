---
layout: post
title:  "Vulnerable Node.js Application"
date:   2020-03-07 16:34:10 -0400
categories: infrastructure 
tags: infrastructure
---
<p>
Getting infrastructure deployed and configured correctly has been one of the most time consuming things when demonstrating the cybersecurity attack chain.
</p>

<h3>Step 1 - Run a Built Container</h3>

<p>
For this particular use case we know that the application is vulnerable and wouldn't plan to leave this container running outside of a Proof of Concept.
</p>

<p>
To run the container;
</p>

<code>
  docker run -p 8080:8080 -d freshdemo/node-simple-rce
</code>
<br>
<br>

<p>
The container details can be found here, <a href="https://github.com/freshdemo/node-simple-rce" target="_blank">https://github.com/freshdemo/node-simple-rce</a>
</p>

