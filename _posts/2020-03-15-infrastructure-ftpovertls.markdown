---
layout: post
title:  "FTP Infrastructure"
date:   2020-03-15 13:24:10 -0400
categories: infrastructure 
tags: infrastructure
---
<p>
Getting infrastructure deployed and configured correctly has been one of the most time consuming things when demonstrating the cybersecurity attack chain.
</p>


<h3>Step 1 - Build the FTP Server</h3>


<h4>Option 1 - Build a Docker Container</h4>

<p>
The primary goal was to deploy this in a Docker container. Reason being it is infrstructure that is not needed all the time, and may need to move from a laptop, to Google Cloud Platform, to Azure, etc.
</p>

<p>
Get it here, <a href="https://github.com/freshdemo/ftpovertls" target="_blank">https://github.com/freshdemo/ftpovertls</a>.
</p>

<h4>Option 2 - Build Your Own</h4>

<p>
You can easily interpret the Dockerfile to deploy all of the software and configurations manually on a host.
</p>

<h4>Option 3 - Run the Built Container</h4>

<p>
From a security perspective it is not a great idea to use an already built container as you have no idea how it was built or if any backdoor or data exfiltration configurations are pre-built. This container is automatically being built on Docker Hub any time a commit is made to GitHub, so what you see is what you get.
</p>

<p>
Download the container, and run it
</p>
<code>
   docker pull freshdemo/ftpovertls<br>
   docker run  -it -p 21:21 -p 30000-30020:30000-30020 -d freshdemo/ftpovertls
</code>
<br>

<h3>Step 2 - Get into the Container</h3>

<p>
By default the FTP server will send the system IP address for passive FTP data transfer. Being in a container this IP address will not work well.
</p>

<p>
After you have issued the docker run command
</p>
<code>
  docker ps -a
</code>
<pre>
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                    PORTS               NAMES
8cb6b5cf2189        17c8452ffa33        "/bin/bash"              3 weeks ago         Exited (0) 37 hours ago                       optimistic_brattain
3dcfa705d425        17c8452ffa33        "/bin/sh -c /root/st…"   3 weeks ago         Exited (0) 3 weeks ago
 pensive_wing
</pre>


<p>
Use the container ID to login to the container
</p>
<code>
  docker exec -it 8cb6b5cf2189 /bin/bash
</code>
<br>

<h3>Step 3 - Change the Public IP</h3>


<p>
From inside the container edit the proftpd.conf file
</p>
<pre>
root@FTP:~# docker exec -it 8cb6b5cf2189 /bin/bash
root@8cb6b5cf2189:/# cd /etc/proftpd/
root@8cb6b5cf2189:/etc/proftpd# vi proftpd.conf
</pre>

<p>
Add the public IP address of your FTP server to the line with <b>MasqueradeAddress</b>on it
</p>

<pre>
MasqueradeAddress               52.138.7.4
</pre>
<br>

<h3>Step 4 (optional) - Change the Passive FTP Ports</h3>


<p>
You can also modify the passive FTP ports from here too if you are in a heavily filtered environment.
</p>
<br>

<h3>Step 5 - Start the FTP Service</h3>

<p>
From within the container run
</p>

<pre>
  root@8cb6b5cf2189:/etc/proftpd# /etc/init.d/proftpd start
[....] Starting ftp server: proftpd2020-08-28 19:35:22,007 8cb6b5cf2189 proftpd[21]: processing configuration directory '/etc/proftpd/conf.d/'
2020-08-28 19:35:22,043 8cb6b5cf2189 proftpd[21] 8cb6b5cf2189: 172.17.0.2:21 masquerading as 52.138.7.4
. ok 
root@8cb6b5cf2189:/etc/proftpd#
</pre>
