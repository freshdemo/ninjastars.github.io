---
layout: post
date:   2021-05-03 17:54:35 -0400
title:  "Gateway HTTP Policies"
categories: infrastructure
tags: gateway
---
<p>
Gateway with WARP offers users privacy online, better performance, and better security. When it comes to security one of the components enterprise clients want to implement is for safe web browsing. They want to ensure their users are permitted to get to sanctioned applications, have some access to tolerated applications, and no access to unsanctioned applications.
</p>

<p>
This post covers what HTTP policies look like in practice, along with a few issues you need to be aware of.
</p>


<h3>Policies for Sanctioned, Tolerated, and Unsanctioned HTTP Applications</h3>


<img src="/images/gateway-http-policy.png">

<p>
The following numbered list walks through each of these policies in an attempt to provide a narrative.
</p>

<ol>
  <li><b>DoNotDecrypt:</b>Decrypting SSL/TLS is critical in getting visibility into how users are accessing applications. There are many applications that can no longer be decrypted, typically due to certificate pinning. If you were to ignore this step most of the applications in this list would generate SSL errors in browsers/apps. There is a category named Do Not Decrypt within Applications to make this easier.
  <li><b>NoDecrypt List:</b>There will typically be more applications used in your environment that can't be decrypted that the decryption vendor will not address for you. As a result it is a good idea to create a list of domains that you also do not want to decrypt in addition to the Applications above. My list includes sites like login.microsoft.com, paypal.com, citrixonline.com, and ~26 others. This is a process that won't actually end. It is important to use this as an opportunity to make policy decisions. For example, is WhatsApp really required for use in your corporate environment? WhatsApp has a massive user base, and can't be decrypted. This could lead to users, willingly or otherwise, to upload data to someone they shouldn't. So rather than not decrypting it to avoid errors, perhaps it should be considered unsanctioned and not permitted at all.
  <li><b>Isolate Security Risks:</b>Browser Isolation allows you to run a browser remotely on the Cloudflare edge. Once the policy is triggered the site will be loaded in the remote browser, the remote browser will drop some JavaScript on the local browser, and the page elements only load in the local browser. This approach can help prevent threats as they are run in the cloud and only the html and images are sent back to the local browser. It can also help with data loss by preventing uploading through the remote browser.
  <li><b>Isloate MSN:</b>Another example of Remote Browser Isolation.
  <li><b>Islolate Slashdot:</b>Another example of Remote Browser Isolation.
  <li><b>Allow GSuite:</b>This is our first sanctioned application policy. This organization allows GSuite and we want to enable traffic for it explicitly so that we can make sure it is allowed being near the top of the policy set, and this policy will allow for easier reporting on what is going on in the GSuite traffic if required.
  <li><b>Block File Sharing:</b>This is our first tolerated application policy. Inside this rule the group of applications for File Sharing is selected which includes all of the usual suspects. These applications are tolerated because we don't care if users want to access their personal files. What we do not want to allow is for users to upload data to unsanctioned applications. The second condition in this policy blocks all Upload MIME types, preventing uploading of any data.
  <li><b>OneDrive Block Uploads:</b>The assumption is that OneDrive would have been captured in the policy above. Several application firewall type solutions have the same issues with similar applications that require a little more attention, and OneDrive is one of those applications. There is no MIME type specified in the actual upload process for this application, so instead we are blocking URL's that contain "oneDrive\.createUploadSession".
  <li><b>Block Outlook:</b>Similar to OneDrive above, Outlook does not get properly identified by our group of applications in the following rule. This is likely because the upload of attachments is sent to outlook.live.com rather than outlook.com. So in this case we specify outlook.live.com as the host. We then create a condition for Upload MIME Type NOT in application/json, application/text, application/plain. Without this condition the page does not load so you do need to allow some data to be able to upload if you want to permit this application. This could lead to a policy decision similar to WhatsApp, where if it is not required for business perhaps you don't allow it at all. Otherwise some closer monitoring is probably a good idea.
  <li><b>Block Email Attachments:</b>For this policy we have selected the group Email, along with a condition HTTP Method is POST. This prevents email attachments on the selected applications.
</ol>

<p>
The rest of the policies are disabled and were there for testing logging everything and blocking everything.
</p>


<h3>Things to be Aware of</h3>

<ol>
  <li>SSL decryption will not work on every application. You need to implement, monitor for errors continuously, tune the DoNotDecrypt policies, and make policy decisions to potentially not allow certain applications.
  <li>For key groups of applications it is important for someone to test the functionality regularly. If you want to make sure there are no uploads permitting on File Sharing, Personal Email, and Collaboration tools, try it. Make certain it works as expected and tune otherwise.
  <li>Monitor for abuse. Create reports that include high risk applications or categories of high risk applications and check regularly for large volumes of data use. If you are not decrypting an application this volume is likely all you will have as an indicator that data has left your control.
</ol>

<h3>TODO</h3>

<p>
Once the Gateway API is exposed I will export the full configurations for the Policies and the SSL Decryption List so that you can implement exactly as described here.
</p>
