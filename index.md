---
layout: post
date:   2021-04-06 12:54:10 -0400
categories:
---
<p>
NinjaStars are here to provide step-by-step (ooh baby) guides that demonstrate stages of the cybersecurity attack chain and how defences can work. Some guides include individual stages of the attack chain and others involve various stages. The goal is to provide engineers with a bag of tools to demonstrate various parts of the Cloudflare platform.
</p>

<p>
Many of the examples include real malicious indicators and could get your computer and computers connected to that computer compromised. To reduce this risk it is recommended to at least use VM's with sharing disabled, and have a good snapshot of the core image. You should also leverage the name server in <a href="https://github.com/freshdemo/mailanddns" target="_blank">freshdemo/mailanddns</a> to contain A records for any of the malicious domains so that you effectively sinkhole them instead of accidentally accessing them.
</p>

<p>
One of the other things we intend to provide is immutible infrastructure so that you can quickly execute the tests in a closed environment, without spending so much time doing system engineering.
</p>

<p>
Each post should be tagged with one of the following, which are alingned with <a href="https://attack.mitre.org/" taget="_blank">Mitre Att&ck</a>. The <a href="/categories/">categories</a> link in the top right corner will bring you to them quickly.
</p>

<ul>
    <li>infrastructure - any systems that will facilitate transmitting the attacks.
    <li>initialaccess
    <li>execution
    <li>persistence
    <li>privilegeescalation
    <li>defenseevasion
    <li>credentialaccess
    <li>discovery
    <li>lateralmovement
    <li>collection
    <li>commandandcontrol
    <li>exfiltration
    <li>impact
 

<!-- Cloudflare Web Analytics --><script defer src='https://static.cloudflareinsights.com/beacon.min.js' data-cf-beacon='{"token": "3ff248322f9d497f8802ebf7d47130c1"}'></script><!-- End Cloudflare Web Analytics -->
