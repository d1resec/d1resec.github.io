---
id: 172
title: 'Securing a Blog: Preventing Clickjacking'
date: 2016-08-11T03:48:14+00:00
author: nwalsh
layout: post
guid: https://nwalsh.me/?p=172
permalink: /?p=172
categories:
  - Securing a Blog
  - Security
tags:
  - apache
  - clickjacking
  - headers
  - security
---
<h1 style="text-align: center;">
  What is Clickjacking?
</h1>

[Clickjacking](https://www.owasp.org/index.php/Clickjacking) is the ability to frame an existing website on an attacker controlled domain. To fool the user, the attacker creates a webpage with inputs that users can click. Unbeknownst to the users, the buttons they are clicking on belong to the transparent iframe of another website. Therefore, attackers can fool users into clicking buttons, inserting text, etc.

<h1 style="text-align: center;">
  How can we protect against Clickjacking?
</h1>

It&#8217;s easy! Modern browsers accept the X-Frame-Options headers which you can use to specify if you want the content on your website framed. There are a few options that we can use in case of special circumstances.

<pre class="">X-Frame-Options: DENY
X-Frame-Options: SAMEORIGIN
X-Frame-Options: ALLOW-FROM https://example.com/
</pre>

We can set the header to: DENY, SAMEORIGIN, or ALLOW-FROM.

  * Deny will prevent ANY website from loading your site in an iframe.
  * SAMEORIGIN will allow only the pages origin (the protocol, host, and port) to frame content.
  * ALLOW-FROM whitelists websites that are able to frame your content. However, be careful for this option as it is not supported in all browsers (Chrome, Edge, IE, etc.). If the browser does not support it then it means that the header does nothing to prevent clickjacking. Therefore, use this option with care.

<p style="text-align: left;">
  For my case, I added the following line to Apache sites-enabled default configuration:
</p>

<pre>Header always append X-Frame-Options SAMEORIGIN</pre>

This means that my website can only be framed by my website, which is a fair secure configuration.