---
id: 165
title: 'Securing a Blog: Enabling HTTP Strict Transport Security (HSTS)'
date: 2016-08-05T17:26:51+00:00
author: nwalsh
layout: post
guid: https://nwalsh.me/?p=165
permalink: /?p=165
categories:
  - Securing a Blog
  - Security
tags:
  - apache
  - HSTS
  - HTTP Strict Transport Security
  - nginx
---
<h1 style="text-align: center;">
  Background
</h1>

[HTTP Strict Transport Security](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security) is a security configuration that will force the browser to always connect to your website over SSL. For a website that operates over unencrypted HTTP and encrypted HTTPS, they usually force a redirect from the HTTP version of the website to the encrypted HTTPS version of the website.

However, a [Man-in-the-Middle attack](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) is possible on the first HTTP request since the browser is not doing any checks on the certificate (like it would do if the content was served over HTTPS). When the browser sees that the certificate being used is untrusted then the browser will show a warning to the user that they might be getting man-in-the-middled. This is if the site is using HTTPS.

[<img class="alignnone size-full wp-image-166" src="https://nwalsh.me/wp-content/uploads/2016/08/ssl_warning.png" alt="Chrome MITM Warning" width="797" height="298" srcset="https://nwalsh.me/wp-content/uploads/2016/08/ssl_warning.png 797w, https://nwalsh.me/wp-content/uploads/2016/08/ssl_warning-300x112.png 300w, https://nwalsh.me/wp-content/uploads/2016/08/ssl_warning-768x287.png 768w, https://nwalsh.me/wp-content/uploads/2016/08/ssl_warning-700x262.png 700w" sizes="(max-width: 797px) 100vw, 797px" />](https://nwalsh.me/wp-content/uploads/2016/08/ssl_warning.png)

<h1 style="text-align: center;">
  Enabling HSTS
</h1>

Enabling HSTS is very easy, usually requiring only a one line change in the Apache or NGINX configuration. The server needs to set a new header when the HTTPS version of the site is requested. The header should be Strict-Transport-Security with an option of max-age and whether to include subdomains or not. We can add the header into our HTTPS VirtualHost with the following line:

<pre>Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"</pre>

Now when the client requests the server over HTTP for the first time, it will redirect to the HTTPS version of the website where this header will be sent. Then, for future requests to the HTTP version of the website, the browser will automatically redirect the user to the HTTPS version (as opposed to the server doing the redirection).

<h1 style="text-align: center;">
  Flow
</h1>

Here is the flow of request/response:

First someone requests the website over HTTP, and the server redirects them to the HTTPS version.

Request:

<pre>GET / HTTP/1.1
Host: nwalsh.me
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:44.0) Gecko/20100101 Firefox/44.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
</pre>

Response:

<pre>HTTP/1.1 302 Found
Date: Fri, 05 Aug 2016 16:57:06 GMT
Server: Apache
Location: https://nwalsh.me/
Content-Length: 202
Connection: close
Content-Type: text/html; charset=iso-8859-1302

Found
The document has moved https://nwalsh.me/
</pre>

Then, after being redirected the server supplies the Strict-Transport-Security header to the browser:

Request for Redirection:

<pre>GET / HTTP/1.1
Host: nwalsh.me
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:44.0) Gecko/20100101 Firefox/44.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: close
</pre>

Response headers for Redirection:

<pre class="">HTTP/1.1 200 OK
Date: Fri, 05 Aug 2016 16:57:20 GMT
Server: Apache
Strict-Transport-Security: max-age=31536000; includeSubDomains
Expires: Wed, 11 Jan 1984 05:00:00 GMT
Cache-Control: no-cache, must-revalidate, max-age=0
Pragma: no-cache
Link: &lt;https://nwalsh.me/?rest_route=/&gt;; rel="https://api.w.org/"
Vary: Accept-Encoding
Content-Length: 51946
Connection: close
Content-Type: text/html; charset=UTF-8
</pre>

Notice how the server supplies our header that we have added to the VirtualHost.

Now, the clients browser has cached our header and it will know to only connect to our server over HTTPS even when given a HTTP link.

Try it on this website! If you request <http://nwalsh.me> your request should not happen over HTTP, instead your browser should redirect you (before requesting the website) and force you to the HTTPS version. This is a very easy addition to your server that will make it a bit more secure. Now the only way for an attacker to MiTM without warning is during the very first visit of someone to your website, or when the header expires (set a large max-age!).