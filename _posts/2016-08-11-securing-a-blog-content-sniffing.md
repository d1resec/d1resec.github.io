---
id: 177
title: 'Securing a Blog: Content Sniffing'
date: 2016-08-11T04:32:03+00:00
author: nwalsh
layout: post
guid: https://nwalsh.me/?p=177
permalink: /?p=177
categories:
  - Securing a Blog
  - Security
tags:
  - apache
  - content sniffing
  - internet explorer
---
I recently got into a discussion about Content Sniffing and wanted to shed more light on what it actually does and how it affects the browser.

<h1 style="text-align: center;">
  What is Content Sniffing?
</h1>

Content sniffing is preformed by the browser to determine the true content of the webpage when a file is to be downloaded. This means that the server takes the user upload (maybe a profile picture) and stores it locally. However, it allows the user to download it as it might be stored in the web root. The true &#8216;content&#8217; might be switched to something else if Internet Explorer decides that the filetype given by the web server does not match the content that was sniffed.

To begin with, a user generates a JPG image which contains HTML code. Say that the uploaded JPG does not undergo any postprocessing (which would remove HTML code!) and is successfully uploaded.  The web server might say that the content of the webpage when visiting a profile picture is image/jpeg however, when inspecting the message (and seeing HTML in the picture) Internet Explorer decides to execute this page instead as HTML. Therefore, any HTML or Javascript code in the JPG image will get executed. You can see that this could easily turn into a Cross Site Scripting problem if someone accesses the download link.

Additionally, browsers will preform Content Sniffing if there is not a Content-Type header in the response, as it is the only way to determine how to display the page. However, we cannot prevent browsers from doing content sniffing when they need to, but we can prevent IE from changing the content type.

<h1 style="text-align: center;">
  How can we prevent IE from doing Content Sniffing?
</h1>

Simply include this header in your Apache or NGINX configuration:

<pre class="">X-Content-Type-Options nosniff</pre>

And IE will not attempt to sniff content.