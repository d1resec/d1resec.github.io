---
id: 91
title: Simple Hashcracking
date: 2015-03-13T05:11:54+00:00
author: nwalsh
layout: post
guid: http://nwalsh.me/?p=91
permalink: /?p=91
categories:
  - CTF
  - General
tags:
  - CTF
---
> Write a script to hack the following script using a dictionary attack. There is a wordlist at www.mieliestronk.com/corncob_lowercase.txt. Submit the plaintext password and your script.
> 
> 39d2b1ae41c609d04d9bbcde036940d8 &#8211;
> 
> [Hint: We used the md5sum command]

To begin with we will first search for the hash on Google (thinking smarter, not harder). By doing so, we are greeted with one search result <a href="https://searchcode.com/codesearch/view/12066802/" target="_blank">here</a>. Now we need to test that this file actually has an md5sum hash of what we need. So we will create a text file with the word &#8216;HI&#8217; and run the md5sum command on it.

[<img class="alignnone size-medium wp-image-92" src="http://nwalsh.me/wp-content/uploads/2015/03/hash-300x44.png" alt="hash" width="300" height="44" srcset="https://nwalsh.me/wp-content/uploads/2015/03/hash-300x44.png 300w, https://nwalsh.me/wp-content/uploads/2015/03/hash.png 301w" sizes="(max-width: 300px) 100vw, 300px" />](http://nwalsh.me/wp-content/uploads/2015/03/hash.png)

We can see that this indeed produces the right output. However, what if we do not want to do it this way and use the provided dictionary file? Since we know that the word must be HI in the file we can understand how to approach this problem.

We must go through the dictionary and run the md5sum command on every word until it returns what we are looking for. The &#8216;trick&#8217; to this anomaly is that it doesn&#8217;t state what case we need to look for. Upon trying lowercase dictionary values, nothing will be found. Thus, it is necessary to search for the uppercase values (and then possibly mixed case if this fails). Additionally, it appears that there might be problem with the file. From running our commands on the file we generated, it has a newline appended to the end (the echo command will submit a newline). Therefore, we must append a newline to every word and THEN run the md5 hash.

<pre>import hashlib

to_find = '39d2b1ae41c609d04d9bbcde036940d8'
with open("corncob_lowercase.txt", "r") as r:
  for line in r:
    md5_lower = hashlib.md5(line.lower().strip() + "\n").hexdigest()
    md5_up = hashlib.md5(line.upper().strip() + "\n").hexdigest()
    if md5_lower == to_find:
      print line.strip()
    elif md5_up == to_find:
      print line.upper().strip()
</pre>

If we run this we will find that the hash is the plaintext string &#8220;HI\n&#8221;.

We have now finished this anomaly!