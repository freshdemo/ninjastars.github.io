---
layout: post
title:  "Damn Vulnerable Web Application Infrastructure"
date:   2020-03-16 13:27:11 -0400
categories: infrastructure 
tags: infrastructure
---
<p>
Getting infrastructure deployed and configured correctly has been one of the most time consuming things when demonstrating the cybersecurity attack chain.
</p>

<p>
The primary goal was to deploy this in a Docker container. Reason being it is infrstructure that is not needed all the time, and may need to move from a laptop, to Google Cloud Platform, to Azure, etc.
</p>


<h3>Option 1 - Run the Built Container</h3>

<p>
From a security perspective it is not a great idea to use an already built container as you have no idea how it was built or if any backdoor or data exfiltration configurations are pre-built. 
</p>
<p>
That being said, this particular container is maintained by the the site owner. The source control link on http://www.dvwa.co.uk/ leads to a Github repository. That Github repository references this prebuilt container on Docker Hub.
</p>

<p>
Download the container, and run it
</p>
<code>
  docker run --rm -it -p 80:80 vulnerables/web-dvwa
</code>
<br>

<h3>Option 2 - Build a Docker Container</h3>


<p>
Get it here, <a href="https://github.com/digininja/DVWA" target="_blank">https://github.com/digininja/DVWA</a>.
</p>
<br> 

<h3>Option 3 - Build Your Own</h3>

<p>
You can easily interpret the Dockerfile to deploy all of the software and configurations manually on a host.
</p>
<br>


<!-- Cloudflare Web Analytics --><script defer src='https://static.cloudflareinsights.com/beacon.min.js' data-cf-beacon='{"token": "3ff248322f9d497f8802ebf7d47130c1"}'></script><!-- End Cloudflare Web Analytics -->
