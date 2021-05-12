---
layout: post
title:  "Data Exfiltration over SaaS (safe application enablement)"
date:   2021-05-12 09:49:25 -0400
categories: exfiltration 
tags: gateway
---
<p>
Whether it is a user taking data backups into their own hands, they are stealing the data, or an adversary got their hands on data they would like a copy of, SaaS often provides a great solution. There are many file sharing applications designed to make uploading easy. And in most environments these applications are very difficult to control, so they are effectively allowed. 
</p>

<p>
This use case is focused on File Sharing applications however the exact same process should be followed for personal email, collaboration tools, and pretty much every other category of application permitted.
</p>

<p>
The goal is to permit uploads and downloads to sanctioned applications, meaning applications where IT has access to the data if required. We will be focussing on the tolerated applications that you may want certain functionality from. For instance we may use OneDrive for file sharing so don't want users uploading to sites like Dropbox or Google Drive, but do we care if they download their partners grocery list from those sites? (we shouldn't).
</p>

<h3>Step 1 - Decrypt TLS Traffic</h3>

<p>
Make sure that SSL decryption is enabled for at least your source/client IP. This is what allows the visibility into what's going on inside the stream.
</p>

<p>In Gateway this is very straight forward. First install the root certificate, https://developers.cloudflare.com/cloudflare-one/connections/connect-devices/warp/install-cloudflare-cert on your endpoints. Second enable TLS decryption under Gateway, Policies, Settings.
</p>

<img src="/images/gateway-decrypt.png">

<h3>Step 2 - Configure Security Policies</h3>

<p>
Typically I leave it to you to configure security policies, but in this use case it's important to be clear about what they look like.
</p>

<p>
For this example Google Workspace is the sanctioned service, and everything else is considered unsanctioned or tolerated.
</p>

<p>
A policy for the Google applications is created to explicitely permit them first.
</p>

<img src="/images/gateway-gsuite.png">

<p>
A policy to match File Sharing apps is created. In this case we are permitting some Upload MIME Types which are required to get most of these applications to load, and blocking everything else.
</p>

<img src="/images/gateway-filesharing.png">

<p>
After using several of these File Sharing applications it became evident that not all worked the same. Microsoft OneDrive as an example was still slipping through and so a custom rule needed to be created specifically for it.
</p>

<img src="/images/gateway-onedrive.png">

<h3>Step 3 - Upload a File</h3>

<p>
From a host connected to WARP or Magic WAN login to Dropbox and OneDrive. Upload a file that ideally you have not already uploaded to the service (more on why later).
</p>

<p>
The errors will vary from one application to another. Some will hang until the session times out, and others like Dropbox will actually indicate there was a server error.
</p>


<h3>Step 4 - View the Results</h3>

<p>
Taking a look at the Gateway logs you can see that Dropbox and OneDrive uploading were both blocked. This means that we safely permitted users to get to those sites and download files, however they would be unable to upload data thereby preventing data loss.
</p>

<img src="/images/gateway-blockdropbox.png">
<br>
<br>
<img src="/images/gateway-blockonedrive.png">
<br>
<br>


<h3>Warnings</h3>

<p>
There were a couple observations made.
</p>

<p>
First: SSL/TLS can't be decrypted in an increasing list of applications. For example, Protonmail can be decrypted using a web browser from a desktop. In the mobile application the certificate is pinned which prevents this type of interception. So on one hand enabling SSL/TLS decryption on everything would break application access, on the other hand not decrypting means that you cannot inspect the traffic which will lead to data loss. Particularly for the personal applications such as WhatsApp you have to ask yourself why that application needs to be permitted if you can't control what's going through it.
</p>

<p>
Second: Microsoft OneDrive seems to be a little more evasive. The original policy above did not prevent uploads to OneDrive. A second policy specifically for OneDrive was create which successfully prevents uploads. The point here is for widly used applications it is highly recommended to periodically test policies to ensure that application filtering is working as expected. This same statement applies to all application and Next-Generation Firewalls.
</p>


