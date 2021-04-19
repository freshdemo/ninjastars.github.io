---
layout: post
title:  "Rate Limiting"
date:   2021-04-16 16:34:10 -0400
categories: core 
tags: ratelimiting
---
<p>
Rate Limiting can be helpful when the capacity of the origin server is still less than the triggers for a DDoS service.
</p>

<p>
Rate Limiting is configured as part of the POC Terraform templates. This guide will walk through manual configuration, along with triggering.
</p>

<h3>Step 1 - Configure Firewall Rules for Capcha Challenge</h3>

<ul>
From Dash navigate to Firewall.
Select Tools from the sub-menu.
Create a Custom Rule.
Give the rule a name "API Rate Limit Demo"
Select http & https, and input a domain proxied through Cloudflare.
Set from the same IP address exceeds 10 requests per 30 seconds.
Methods can be set to any.
Orogin Response codes set to 301, 429, 202, 201, 200
Then Block for 1 minute
Optionally configure bypass URLs
</ul>


<p>
Select Save at the bottom. It should look something like this.
</p>
<br><br>
<img src ="/images/ratelimiting.png">

<br>
<br>

<h3>Step 2 - Generate Traffic</h3>

<p>
This script will hit the target URL proxied through Cloudflare 20 times. The connections should get blocked with an error 429 after the first 10 requests.
<p>
<br>
<br>

<pre>
for i in $(seq 1 20); do curl -sIL "https://api.example.com" | grep -i '^HTTP'; done
</pre>

<br>
<br>

<p>
Following is what the tool looks like when run.
</p>

<pre>

stephen@C02F30Q0ML85 kickscript-master@4c529c6663a % for i in $(seq 1 20); do curl -sIL "https://httpbin.perciballi.ca" | grep -i '^HTTP'; done

HTTP/2 200 
HTTP/2 200 
HTTP/2 200 
HTTP/2 200 
HTTP/2 200 
HTTP/2 200 
HTTP/2 200 
HTTP/2 200 
HTTP/2 200 
HTTP/2 200 
HTTP/2 200 
HTTP/2 200 
HTTP/2 200 
HTTP/2 200 
HTTP/2 429 
HTTP/2 429 
HTTP/2 429 
HTTP/2 429 
HTTP/2 429 
HTTP/2 429 

</pre>

<br>
<br>
<h3>Step 3 - Analytics</h3>

<p>
Demonstrate the visualizations that come with the service.
</p>
<p>
From the Tools menu you will see "Activity last 24hr" on your rule. This is a hyperlink that takes you to the Overview menu.
</p>

<img src="/images/ratelimiting-analytics.png">
