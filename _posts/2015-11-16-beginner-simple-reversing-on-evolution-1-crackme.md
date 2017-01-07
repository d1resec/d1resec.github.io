---
id: 115
title: Beginner Simple Reversing on Evolution 1 Crackme
date: 2015-11-16T08:27:00+00:00
author: nwalsh
layout: post
guid: http://nwalsh.me/?p=115
permalink: /?p=115
categories:
  - Crackme
tags:
  - crackme
  - engineering
  - reverse
  - reverse engineering
---
Let&#8217;s look at how we can solve a simple [crackme](https://en.wikipedia.org/wiki/Crackme) titled &#8220;[evolution1](http://www.crackmes.de/users/geeko/evolution1/)&#8220;. This crackme will be <span style="text-decoration: underline;">very</span> simple, and since I am a beginner at reversing, I think that&#8217;s a good thing.

This crackme is made for Windows and can be downloaded by registering an account and downloading through the [crackmes.de](http://www.crackmes.de/users/geeko/evolution1/) website. The first thing we do is download the ZIP and get our debugging environment set up. To be able to analyze the assembly code that the program produces, it is necessary to view it in some other tools. For this crackme, I am going to be using [OllyDGB](http://www.ollydbg.de/) because it is free for Windows and widely recommended.

With both Olly and the evol1 program, we can begin to reverse it. We first run the
  
`evol1` binary to see what it wants of us.

<p style="text-align: center;">
  <a href="https://nwalsh.me/wp-content/uploads/2015/11/auth.png"><img class="alignnone size-full wp-image-116" src="https://nwalsh.me/wp-content/uploads/2015/11/auth.png" alt="auth" width="286" height="142" /></a>
</p>

<p style="text-align: left;">
  To move forward into the program, it is necessary for the user to enter the correct password. So, now that we know what we need to do (find the right password!), let&#8217;s move forward with Olly to analyze the code.
</p>

<p style="text-align: left;">
  Run Olly, either as a normal user or an Administrator. Next, I like to set up Olly to view the executable modules. This can be done multiple ways but you can use the shortcut <code>Alt-E</code> to open the menu, or alternatively hit the blue E near the top of the GUI. This window will show us all the executing programs that we are currently running. Let&#8217;s open <code>evol1</code> using Olly. This can be done by hitting <code>F3</code> and then browsing to your <code>evol1</code> directory. When you are done you should see something similar to this:
</p>

<p style="text-align: center;">
  <a href="http://nwalsh.me/wp-content/uploads/2015/11/olly_11.png"><img class="wp-image-118 size-large aligncenter" src="https://nwalsh.me/wp-content/uploads/2015/11/olly_11-1024x738.png" alt="olly_1" width="625" height="450" srcset="https://nwalsh.me/wp-content/uploads/2015/11/olly_11-1024x738.png 1024w, https://nwalsh.me/wp-content/uploads/2015/11/olly_11-300x216.png 300w, https://nwalsh.me/wp-content/uploads/2015/11/olly_11-624x450.png 624w, https://nwalsh.me/wp-content/uploads/2015/11/olly_11.png 1351w" sizes="(max-width: 625px) 100vw, 625px" /></a>
</p>

<p style="text-align: left;">
  Olly has opened the program but put it in it&#8217;s &#8216;paused&#8217; state. Therefore, to move on we need to hit the play button. When we hit the play button once, the execution stops right before the entry point to <code>evol1</code> main code. Therefore, we need to hit the play button twice.
</p>

<p style="text-align: left;">
  Now we get the prompt from <code>evol1</code> to appear above Olly.
</p>

<p style="text-align: left;">
  <a href="http://nwalsh.me/wp-content/uploads/2015/11/olly_2.png"><img class="wp-image-119 size-large aligncenter" src="https://nwalsh.me/wp-content/uploads/2015/11/olly_2-1024x825.png" alt="olly_2" width="625" height="504" srcset="https://nwalsh.me/wp-content/uploads/2015/11/olly_2-1024x825.png 1024w, https://nwalsh.me/wp-content/uploads/2015/11/olly_2-300x242.png 300w, https://nwalsh.me/wp-content/uploads/2015/11/olly_2-624x503.png 624w, https://nwalsh.me/wp-content/uploads/2015/11/olly_2.png 1073w" sizes="(max-width: 625px) 100vw, 625px" /></a>
</p>

<p style="text-align: left;">
  We don&#8217;t put anything into the prompt. Instead we are going to investigate and see what the code is checking for our input. We right-click on the executable module with the path to our running <code>evol1</code>, and choose to view code in CPU.
</p>

<p style="text-align: left;">
  <a href="http://nwalsh.me/wp-content/uploads/2015/11/olly_3.png"><img class="aligncenter wp-image-120 size-full" src="https://nwalsh.me/wp-content/uploads/2015/11/olly_3.png" alt="olly_3" width="666" height="389" srcset="https://nwalsh.me/wp-content/uploads/2015/11/olly_3.png 666w, https://nwalsh.me/wp-content/uploads/2015/11/olly_3-300x175.png 300w, https://nwalsh.me/wp-content/uploads/2015/11/olly_3-624x364.png 624w" sizes="(max-width: 666px) 100vw, 666px" /></a>
</p>

<p style="text-align: left;">
  You will see your window shift to the <code>evol1</code> module and we are now looking at the code for it. One simple check to see where we need to be at is to view all the strings in the program and see if we can find the &#8220;password please&#8221; prompt in the code. To do so we right click on the assembly code and then go to &#8220;Search for&#8221; > &#8220;All referenced text strings&#8221;.
</p>

<p style="text-align: left;">
  <a href="http://nwalsh.me/wp-content/uploads/2015/11/olly_4.png"><img class="aligncenter wp-image-121 size-full" src="https://nwalsh.me/wp-content/uploads/2015/11/olly_4.png" alt="olly_4" width="993" height="670" srcset="https://nwalsh.me/wp-content/uploads/2015/11/olly_4.png 993w, https://nwalsh.me/wp-content/uploads/2015/11/olly_4-300x202.png 300w, https://nwalsh.me/wp-content/uploads/2015/11/olly_4-624x421.png 624w" sizes="(max-width: 993px) 100vw, 993px" /></a>
</p>

<p style="text-align: left;">
  When we do this a new menu pops up with mainly ASCII strings.
</p>

<p style="text-align: center;">
  <a href="http://nwalsh.me/wp-content/uploads/2015/11/olly_5.png"><img class="alignnone wp-image-122 size-full" src="https://nwalsh.me/wp-content/uploads/2015/11/olly_5.png" alt="olly_5" width="987" height="644" srcset="https://nwalsh.me/wp-content/uploads/2015/11/olly_5.png 987w, https://nwalsh.me/wp-content/uploads/2015/11/olly_5-300x196.png 300w, https://nwalsh.me/wp-content/uploads/2015/11/olly_5-624x407.png 624w" sizes="(max-width: 987px) 100vw, 987px" /></a>
</p>

<p style="text-align: left;">
  We can see a lot of references to <a href="https://en.wikipedia.org/wiki/Delphi_(programming_language)">Borland/Delphi</a> which is what this program was coded in. Additionally, there are a lot of strings that don&#8217;t really apply to this program. This is because they are used by the compiler or were imported in from other libraries. It makes sense that the code we are interested might be near the bottom because all other imports are going to be compiled before the custom code. Therefore, we scroll near the bottom and look for the &#8216;password please&#8217; text.
</p>

<p style="text-align: center;">
  <a href="http://nwalsh.me/wp-content/uploads/2015/11/olly_6.png"><img class="alignnone wp-image-123 size-full" src="https://nwalsh.me/wp-content/uploads/2015/11/olly_6.png" alt="olly_6" width="987" height="149" srcset="https://nwalsh.me/wp-content/uploads/2015/11/olly_6.png 987w, https://nwalsh.me/wp-content/uploads/2015/11/olly_6-300x45.png 300w, https://nwalsh.me/wp-content/uploads/2015/11/olly_6-624x94.png 624w" sizes="(max-width: 987px) 100vw, 987px" /></a>
</p>

<p style="text-align: left;">
  We end up finding it. In addition, there are two other strings. One is &#8216;authentication&#8217; which is the title of the authentication prompt. However, the other string &#8216;mypassword&#8217; isn&#8217;t anything we are familiar with, and additionally it seems like a password. Let&#8217;s try to enter this as a password and see what happens.
</p>

<p style="text-align: center;">
  <a href="http://nwalsh.me/wp-content/uploads/2015/11/olly_7.png"><img class="alignnone size-full wp-image-124" src="https://nwalsh.me/wp-content/uploads/2015/11/olly_7.png" alt="olly_7" width="288" height="139" /></a>
</p>

<p style="text-align: left;">
  We are taken to a new window that we did not see when we entered a wrong password!
</p>

<p style="text-align: center;">
  <a href="http://nwalsh.me/wp-content/uploads/2015/11/olly_8.png"><img class="alignnone size-medium wp-image-125" src="https://nwalsh.me/wp-content/uploads/2015/11/olly_8-300x290.png" alt="olly_8" width="300" height="290" srcset="https://nwalsh.me/wp-content/uploads/2015/11/olly_8-300x290.png 300w, https://nwalsh.me/wp-content/uploads/2015/11/olly_8.png 470w" sizes="(max-width: 300px) 100vw, 300px" /></a>
</p>

<p style="text-align: left;">
  And thus, this crackme is solved by finding the plaintext password in the Windows executable. For reverse engineering, this is my very first crackme that I have finished, and though it was simple, there was a lot to learn about using OllyDGB.
</p>