---
id: 188
title: How to Takeover Subdomains
date: 2016-09-02T22:16:22+00:00
author: nwalsh
layout: post
guid: https://nwalsh.me/?p=188
permalink: /?p=188
categories:
  - General
  - Security
tags:
  - DNS
  - hijacking
  - subdomain takeover
---
I have been scouring the web for a good subdomain takeover tutorial but have come up short. This space apparently has not been researched publicly so I wanted to shed some light on what I have found.

## Understanding Subdomain Takeover

A prerequisite for the vulnerability must exist.

That is: a DNS record was created at one point that points a subdomain name to either a third-party website, or third-party IP.

For instance, say that a company has created an application on Heroku, they would be able to host it on Heroku under _companyname.herokuapp.com_. Now let&#8217;s say they would like users to be able to navigate to _app.companyname.com_ instead of redirecting to _companyname.herukoapp.com_. They create a CNAME record for _app.companyname.com_ which points to _companyname.herokuapp.com_ and now when you load the page _app.companyname.com_ you are actually loading the content from _companyname.herokuapp.com_ but the browser does not change location in the URL, so we are still on the same domain, sweet.

Now that we have this record pointing to a third-party, the administrators must make sure that they do not let their application expire. Let&#8217;s say they decide Heroku sucks and they are going to move it internally, but they forgot that there was a CNAME pointing to that content. Now, anybody can create a Heroku application and request the subdomain companyname.herokuapp.com. Heroku sees that no one else owns this name, so it is valid and therefore the user is given the domain. Now, whenever someone goes to app.companyname.com the brand new Heroku application is displayed on the page. At this point the attacker can create forms to trick the user that they are interacting with companyname.com. This was an example of a CNAME being incorrect, but could also happen with IP addresses, in that case the attacker has to request the IP address specifically from a host and hope they receive it.

## How can we find these vulnerabilities?

_The first thing that you can look for is a name server that will provide a zone transfer. Now a days this is very difficult to find, so it won&#8217;t be explored here. Otherwise it will speed up the process I will explain below._

The first thing we will need to do is to get all subdomains of a domain. There are plenty of tools that can help us in this regard. The fastest one I have found is [subbrute.](https://github.com/TheRook/subbrute)

Next, we will need to get the records for all of these subdomains and determine if they have a record that points to a third party website or IP address. We can determine if they point to a third party website by parsing the DNS logs when we do a lookup of that domain. For instance, if we see a CNAME in the response, then we extract where it is pointing, and then check to see if that URL ends with the normal website domain. If it does not, then we can alert on this for further analysis.

_Subbrute does this automatically if you pass the option &#8216;-p&#8217;._

## What does a vulnerable CNAME look like?

<div id="attachment_198" style="width: 310px" class="wp-caption aligncenter">
  <a href="https://nwalsh.me/wp-content/uploads/2016/09/no_such_app.png"><img class="size-medium wp-image-198" src="https://nwalsh.me/wp-content/uploads/2016/09/no_such_app-300x90.png" alt="Vulnerable Heroku Instance" width="300" height="90" srcset="https://nwalsh.me/wp-content/uploads/2016/09/no_such_app-300x90.png 300w, https://nwalsh.me/wp-content/uploads/2016/09/no_such_app.png 573w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Vulnerable Heroku Instance
  </p>
</div>

There are a few common websites that do not do verification checks on requested subdomains which means anyone can request one if it is not already taken. Detectify has a good list of these [here.](https://labs.detectify.com/2014/10/21/hostile-subdomain-takeover-using-herokugithubdesk-more/) Check the address in the CNAME field and determine if it was one of the websites that is in the link. If it is, navigate to the **subdomain of the website** **not the CNAME.** When you navigate to the subdomain if there is an indicator that there is no application, then the page is vulnerable. You can then attempt to sign up for that subdomain through whatever third party is being used.

_If you navigate to the CNAME there are instances where it might look vulnerable but really is not. These applications can determine if the browser&#8217;s Host header is from the expected domain, and return a different page if it is not. There have been people reporting this as bugs but it is clearly not if you navigate to the subdomain on the website. See [this](https://hackerone.com/reports/175397) report.
  
_ 

Therefore one can follow these steps:

  1. Get domain name of target (not hard)
  2. Run subbrute or any other tool on that target
  3. Check if any subdomain returns a CNAME to a 3rd party service
  4. Navigate to that subdomain
  5. If the subdomain appears to be claimable, then **attempt to claim it**.
  6. If you successfully claim the subdomain and are able to host content, then it is definitely vulnerable.

&nbsp;

&nbsp;