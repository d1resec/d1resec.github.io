---
id: 133
title: 'Securing a Blog: Using LetsEncrypt to Upgrade to HTTPS'
date: 2016-01-02T22:07:11+00:00
author: nwalsh
layout: post
guid: http://nwalsh.me/?p=133
permalink: /?p=133
categories:
  - Securing a Blog
tags:
  - HTTP
  - HTTPS
  - letsencrypt
  - upgrade HTTP
---
[<img class="aligncenter" src="https://letsencrypt.org/images/letsencrypt-logo-horizontal.svg" alt="" width="339" height="81" />](https://letsencrypt.org/images/letsencrypt-logo-horizontal.svg)

The site now redirects all HTTP requests to HTTPS by default. To get the certificate I used [LetsEncrypt](https://letsencrypt.org/howitworks/). LetsEncrypt is a new Certificate Authority (CA) that provides free certificates. This means that websites can now change to HTTPS without incurring costs of having a certificate signed by another CA.

<h1 style="text-align: center;">
  Using LetsEncrypt
</h1>

Let&#8217;s take a look at how to use LetsEncrypt to move our website to HTTPS. Since this website is being served by Apache, this means that we can actually use the built-in apache flag when we run LetsEncrypt. This means that it will generate the certificates, update the configuration files, and get everything running to its best abilities. Know that LetsEncrypt is in beta so you might see some funky things that happen when you run it.

In my case, I downloaded the code and then ran the application for apache.

<pre>user@webserver:~$ git clone https://github.com/letsencrypt/letsencrypt
user@webserver:~$ cd letsencrypt
user@webserver:~$ ./letsencrypt-auto --apache</pre>

You will have to put in the details for the certificate. There is the website name that you want to encrypt, and you also have to put some more information incase you want to reset your configuration as the files are protected with a password. Therefore, LetsEncrypt needs your email address if you want to reset.

When restarting Apache, my website was definitely broken. Here are the errors from the apache error.log file:

<pre>[Fri Jan 01 14:46:22.858617 2016] [ssl:emerg] [pid 27013] AH02312: Fatal error initialising mod_ssl, exiting.
[Fri Jan 01 14:50:03.405717 2016] [ssl:emerg] [pid 27078] AH02240: Server should be SSL-aware but has no certificate configured [Hint: SSLCertificateFile] ((null):0)
</pre>

So, using LetsEncrypt wasn&#8217;t as easy as I was hoping it would be. Perhaps it was my configurations that I already had created, or it was the fact the application is in beta and didn&#8217;t correctly identify everything on my server. Anyways, when looking up this error, it seems that the Apache VirtualHost wasn&#8217;t able to find the SSL certificate information. Therefore, we can tell it where to find it. First we edit the file containing our VirtualHosts,

<pre>user@webserver:~$ vim /etc/apache2/sites-enabled/default-000.conf</pre>

Then we provide the flags to find the SSL information for the VirtualHost *:443. The files are located by default at /etc/letsencrypt/live/website_name/.

<pre>&lt;VirtualHost *:443&gt;
SSLEngine on
SSLCertificateFile /etc/letsencrypt/live/nwalsh.me/cert.pem
SSLCertificateKeyFile /etc/letsencrypt/live/nwalsh.me/privkey.pem
SSLCACertificateFile /etc/letsencrypt/live/nwalsh.me/chain.pem
&lt;/VirtualHost&gt;
</pre>

Then I also had to specify some options for the 443 Virtual Host that were not previously specified. These were namely:

<pre>ServerAdmin
ServerName
ServerAlias
DocumentRoot
</pre>

Additionally, the path for DocumentRoot was set incorrectly. When finally setting these correctly I had a website that worked! The HTTP automatically redirects to HTTPS, and LetsEncrypt will handle that for you with their client.

Overall, I didn&#8217;t previously use SSL because I didn&#8217;t want to pay for a certificate, this means that when LetsEncrypt started to gain traction I knew I had to try it out. It&#8217;s perfect for those who only have a couple of websites and don&#8217;t want to pay for a certificate.