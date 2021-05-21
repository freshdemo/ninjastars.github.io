---
layout: post
title:  "DNS Data Exfiltration (encoded subdomains)"
date:   2021-04-03 15:43:25 -0400
categories: exfiltration 
tags: gateway bypass
---
<p>
Once an environment has been compromised and the adversary finds some desirable data the data needs to be removed from the network. In this use case we will assume that common protocols like FTP and SSH are heavily restricted on egress traffic (as they should be). One way to move the data is to encode it, making it harder to identify, then split it up into chunks, and apply those chunks as subdomains. So when we even resolve these subdomains they go to the adversary who can stitch them back together and decode them.i
</p>

<h3>Step 1 - Check out the data</h3>

<p>
We have some sample data in <a href="https://github.com/freshdemo/dnstunnel-subdomains/blob/master/dns_data.txt">https://github.com/freshdemo/dnstunnel-subdomains/blob/master/dns_data.txt</a>.
</p>

<p>
It's base64 encoded so use your favorite decoder to see what the secrets are.
</p>

<h3>Step 2 - Run the "Script"</h3>

<p>
With the data already encoded, we have also chopped it up into subdomains, and created a script to resolve them all.
</p>

<p>
The powershell version for Windows can be found <a href="https://github.com/freshdemo/dnstunnel-subdomains/blob/master/dns_tunneling.ps1">https://github.com/freshdemo/dnstunnel-subdomains/blob/master/dns_tunneling.ps1</a>
</p>

<p>
The Mac/Linux version can be found <a href="https://github.com/freshdemo/dnstunnel-subdomains/blob/master/dns-tunnel.sh">https://github.com/freshdemo/dnstunnel-subdomains/blob/master/dns-tunnel.sh</a>
</p>

<h3>Step 3 - View the Results</h3>

<p>
The client may not provide a lot of output because all it has to do is try to resolve the domains.
</p>

<p>
Looking at the following log we can see that the solution prevented the session. It determined that the traffic is attempting to tunnel data in DNS and sinkholed the session. Sinkholing means that the DNS resolution response was modified in transit so that the client doesn't even receive the malicious IP address.
</p>
<br>
<br>
<img src="/images/exfiltration-dnstunneling-subdomain.png">



