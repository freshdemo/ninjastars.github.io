---
layout: post
title:  "WARP Client SSL Decryption Certificate on Linux & Container"
date:   2021-04-21 13:24:10 -0400
categories: infrastructure 
tags: gateway
---
<p>
WARP has the ability to decrypt TLS traffic in order to provive visibility into traffic. With this functionality you can better control HTTP applications, control files, and control malware.
</p>

<p>
If you are running Linux in a VM or container on your host running WARP this post will walk you through how to deploy the certificate in the VM/container so that all the CLI tools will continue to work without getting an SSL error.
</p>

<p>
Prior to deploying the certificate command line tools will give SSL errors. If you were to run a command like <code>openssl s_client  -connect bagof.ninjastars.cf:443</code> you will get an error like this;
</p>

<pre>
CONNECTED(00000003)
depth=1 C = US, ST = California, L = San Francisco, O = "Cloudflare, Inc.", OU = Gateway Intermediate ECC Certificate Authority
verify error:num=20:unable to get local issuer certificate
verify return:1
depth=0 CN = ninjastars.cf
verify return:1
---
</pre>

<h3>Step 1 - Get the Certificate</h3>

<p>
Get this cert onto the system in whichever way you can, <a href="https://developers.cloudflare.com/cloudflare-one/9922abcf75b55a3bb68a8e42ebe5c4a5/Cloudflare_CA.crt">https://developers.cloudflare.com/cloudflare-one/9922abcf75b55a3bb68a8e42ebe5c4a5/Cloudflare_CA.crt</a>
</p>
<ul>
 <li>curl -o Cloudflare_CA.crt https://developers.cloudflare.com/cloudflare-one/9922abcf75b55a3bb68a8e42ebe5c4a5/Cloudflare_CA.crt</li>
 <li>wget https://developers.cloudflare.com/cloudflare-one/9922abcf75b55a3bb68a8e42ebe5c4a5/Cloudflare_CA.crt</li>
 <li>docker cp Cloudflare_CA.crt containername:/path/where/you/want/it</li>
</ul>

<h3>Step 2 - Convert Format</h3>

<p>
The certificate is delivered in DER format, which is a binary encoded X509. Linux typically wants plain text format which is what this step provies.
</p>

<pre>
  openssl x509 -in Cloudflare_CA.crt -inform DER -out Cloudflare_CA.cert
</pre>

<p>
You can <code>cat</code> each of these files to see that the new .cert file is now in plain text.
</p>

<h3>Step 3 - Move the Certificate</h3>

<p>
To get the system to import the certificate, move it into the appropriate directory.
</p>

<p>
Be sure to name the extension of your decoded certificate .crt. Also note that the destination path for your certificate may be /usr/share/ca-certificates.
</p>

<pre>
  cp Cloudflare_CA.cert /usr/local/share/ca-certificates/CloudflareCA.crt
</pre>


<h3>Step 4 - Import the Certificate</h3>

<p>
The following command will import the certificate into the system trust store.
</p>

<pre>
  update-ca-certificates
</pre>


<h3>Step 5 - Validate the Change</h3>

<p>
Re-run the openssl command above, <code>openssl s_client  -connect bagof.ninjastars.cf:443</code> and you will get a more positive response that does not include the verify error.
</p>

<pre>
CONNECTED(00000003)
depth=2 C = IE, O = Baltimore, OU = CyberTrust, CN = Baltimore CyberTrust Root
verify return:1
depth=1 C = US, O = "Cloudflare, Inc.", CN = Cloudflare Inc ECC CA-3
verify return:1
depth=0 C = US, ST = California, L = San Francisco, O = "Cloudflare, Inc.", CN = bagof.ninjastars.cf
verify return:1
</pre>
<!-- Cloudflare Web Analytics --><script defer src='https://static.cloudflareinsights.com/beacon.min.js' data-cf-beacon='{"token": "3ff248322f9d497f8802ebf7d47130c1"}'></script><!-- End Cloudflare Web Analytics -->
