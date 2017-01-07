---
id: 135
title: 'Securing a Blog: Using WPScan to Scan WordPress'
date: 2016-01-05T04:34:27+00:00
author: nwalsh
layout: post
guid: https://nwalsh.me/?p=135
permalink: /?p=135
categories:
  - Securing a Blog
  - Security
tags:
  - scan wordpress
  - Securing WordPress
  - wordpress
  - wpscan
---
[<img class="aligncenter" src="https://images.duckduckgo.com/iu/?u=https%3A%2F%2Fsecure.rosehosting.com%2Fblog%2Fwp-content%2Fuploads%2F2015%2F02%2Fwpscan.png&f=1" alt="" width="300" height="300" />](https://images.duckduckgo.com/iu/?u=https%3A%2F%2Fsecure.rosehosting.com%2Fblog%2Fwp-content%2Fuploads%2F2015%2F02%2Fwpscan.png&f=1)

Here is a small tutorial that will help you audit your WordPress installation to see if there are any glaring misconfigurations or exploits.

<h1 style="text-align: center;">
  What is WPScan?
</h1>

[WPScan](https://github.com/wpscanteam/wpscan) is a Ruby gem that scans WordPress for any version numbers, plugins, or themes which are vulnerable to exploits. There are many publicly available databases of WordPress exploits, and it appears that WPScan uses [this database](https://wpvulndb.com/) to find exploits to match any information that it finds. To find information, WPScan will browse files like readme&#8217;s and traverse directories to find plugin or theme information.

<h1 style="text-align: center;">
  Using WPScan
</h1>

Let&#8217;s install wpscan to get started. I have a machine running Ubuntu, so we will be following instructions for that. The instructions can be found on the [Github page](https://github.com/wpscanteam/wpscan#install) for wpscan for each distro that you are using. Using the instructions provided on the Github page was enough to get everything working correctly so lets see what it can do. We need to look at the [arguments](https://github.com/wpscanteam/wpscan#wpscan-arguments) that the command line can take so that we can accurately test our WordPress installation.

It seems that we must supply the `--url` flag to specify the host we want to test, `--follow-redirection`  [since this site redirects to HTTPS](https://nwalsh.me/?p=133), and then the `--enumerate` flag so that we can get an intensive, general scan.

<pre>nwalsh@artemis2:~/wpscan$ ruby wpscan.rb --url nwalsh.me --follow-redirection --enumerate
_______________________________________________________________
        __          _______   _____
        \ \        / /  __ \ / ____|
         \ \  /\  / /| |__) | (___   ___  __ _ _ __
          \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
           \  /\  /  | |     ____) | (__| (_| | | | |
            \/  \/   |_|    |_____/ \___|\__,_|_| |_|

        WordPress Security Scanner by the WPScan Team
                       Version 2.9
          Sponsored by Sucuri - https://sucuri.net
   @_WPScan_, @ethicalhack3r, @erwan_lr, pvdl, @_FireFart_
_______________________________________________________________

Following redirection https://nwalsh.me/
[+] URL: https://nwalsh.me/
[+] Started: Mon Jan  4 22:59:15 2016

[!] The WordPress 'https://nwalsh.me/readme.html' file exists exposing a version number
[+] Interesting header: LINK: &lt;https://nwalsh.me/?rest_route=/&gt;; rel="https://api.w.org/"
[+] Interesting header: SERVER: Apache
[+] Interesting header: X-POWERED-BY: PHP/5.5.9-1ubuntu4.14
[+] XML-RPC Interface available under: https://nwalsh.me/xmlrpc.php

[+] WordPress version 4.4 identified from meta generator

[+] WordPress theme in use: twentytwelve - v1.9

[+] Name: twentytwelve - v1.9
 |  Latest version: 1.9 (up to date)
 |  Location: https://nwalsh.me/wp-content/themes/twentytwelve/
 |  Readme: https://nwalsh.me/wp-content/themes/twentytwelve/readme.txt
 |  Style URL: https://nwalsh.me/wp-content/themes/twentytwelve/style.css
 |  Theme Name: Twenty Twelve
 |  Theme URI: https://wordpress.org/themes/twentytwelve/
 |  Description: The 2012 theme for WordPress is a fully responsive theme that looks great on any device. Features...
 |  Author: the WordPress team
 |  Author URI: https://wordpress.org/

[+] Enumerating installed plugins (only ones with known vulnerabilities) ...
</pre>

So, for my website there aren&#8217;t any glaring vulnerabilities but there are a few things that we could do to make the site harder to enumerate. The first thing we want to do is follow up on the
  
<code style="line-height: 1.714285714;">[!] The WordPress 'https://nwalsh.me/readme.html' file exists exposing a version number</code>
  
 <span style="line-height: 1.714285714; font-size: 1rem;">finding. This is as simple as navigating to the web path and removing the readme file. There isn&#8217;t a real purpose to have these files in production, so I will remove it.</span>
  
`nwalsh@artemis2:~/wpscan$ sudo rm /var/www/readme.html`
  
That should remove the file.

Let&#8217;s look at some of the &#8216;Interesting Headers&#8217;.
  
`[+] Interesting header: LINK: <https://nwalsh.me/?rest_route=/>; rel="https://api.w.org/"`
  
Appears to be the WordPress REST API that clients can use to browse the website. I was not aware WordPress came with a preinstalled API but it isn&#8217;t anything to worry about.
  
`[+] Interesting header: SERVER: Apache`
  
This is something that we cannot change from Apache, the only option is to recompile and put our own header, or install mod_security. I&#8217;m not too interested in doing either of those so let&#8217;s move on.
  
`Interesting header: X-POWERED-BY: PHP/5.5.9-1ubuntu4.14`
  
This is something that PHP sets in it&#8217;s `php.ini` file. We can search for the file and set the variable `php_expose` to off.

<pre>user@webserver:~/wpscan$ locate php.ini
/etc/php5/apache2/php.ini
</pre>

We need to edit the `/etc/php5/apache2/php.ini` file and make sure that the line `php_expose = off` is set.

Finally there is the XML-RPC finding. This file can cause a [Pingback DOS attack](https://isc.sans.edu/forums/diary/Wordpress+Pingback+DDoS+Attacks/17801), so make sure to remove this file as well. It is as easy as navigating to your wordpress directory and running `rm xmlrpc.php`

The new scan:

<pre>nwalsh@artemis2:~/wpscan$ ruby wpscan.rb --url nwalsh.me --follow-redirection
_______________________________________________________________
        __          _______   _____
        \ \        / /  __ \ / ____|
         \ \  /\  / /| |__) | (___   ___  __ _ _ __
          \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
           \  /\  /  | |     ____) | (__| (_| | | | |
            \/  \/   |_|    |_____/ \___|\__,_|_| |_|

        WordPress Security Scanner by the WPScan Team
                       Version 2.9
          Sponsored by Sucuri - https://sucuri.net
   @_WPScan_, @ethicalhack3r, @erwan_lr, pvdl, @_FireFart_
_______________________________________________________________

Following redirection https://nwalsh.me/
[+] URL: https://nwalsh.me/
[+] Started: Mon Jan  4 23:31:31 2016

[+] Interesting header: LINK: &lt;https://nwalsh.me/?rest_route=/&gt;; rel="https://api.w.org/"
[+] Interesting header: SERVER: Apache

[+] WordPress version 4.4 identified from meta generator

[+] WordPress theme in use: twentytwelve - v1.9

[+] Name: twentytwelve - v1.9
 |  Latest version: 1.9 (up to date)
 |  Location: https://nwalsh.me/wp-content/themes/twentytwelve/
 |  Readme: https://nwalsh.me/wp-content/themes/twentytwelve/readme.txt
 |  Style URL: https://nwalsh.me/wp-content/themes/twentytwelve/style.css
 |  Theme Name: Twenty Twelve
 |  Theme URI: https://wordpress.org/themes/twentytwelve/
 |  Description: The 2012 theme for WordPress is a fully responsive theme that looks great on any device. Features...
 |  Author: the WordPress team
 |  Author URI: https://wordpress.org/

[+] Enumerating plugins from passive detection ...
[+] No plugins found

[+] Finished: Mon Jan  4 23:31:36 2016
[+] Requests Done: 39
[+] Memory used: 9.254 MB
[+] Elapsed time: 00:00:04
</pre>

Although it doesn&#8217;t look like much has changed, our website was already pretty good from a wpscan perspective as no plugins or themes were found to be vulnerable.