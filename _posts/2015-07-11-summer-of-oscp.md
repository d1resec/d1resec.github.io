---
id: 102
title: 'Summer of OSCP: A Review'
date: 2015-07-11T06:25:17+00:00
author: nwalsh
layout: post
guid: http://nwalsh.me/?p=102
permalink: /?p=102
categories:
  - General
tags:
  - OSCP
  - review
---
<h1 style="text-align: center;">
  Intro
</h1>

This summer I took the Penetration Testing with Kali Linux course provided by Offensive Security. I had heard a lot of good things about the course and the certification mainly due to how hands-on the course was. I was a bit concerned that I would be behind in the course and struggling to keep up due to the reviews that I have read beforehand on the course. This was due to the fact that the reviewers said how hard and trying the course can be, especially if you are not prepared beforehand. However, I decided to pursue the certification and bought the 90 days of lab time, hoping that I would be prepared in time for the exam.

<h1 style="text-align: center;">
  Course Materials
</h1>

When my lab date came I downloaded all the course materials and the Kali Linux virtual machine that was provided. I **highly** recommend any who are going to do the course to use Offensive Security&#8217;s image, it will save you headaches and you will be able to follow along with the course materials easier than configuring a lot of tools manually, and running into strange errors that take up valuable lab time to resolve. Going through the course materials, I would watch the videos for the module, and then read the course manual associated with it. As a result, I was able to simply follow along with the videos and the course manual to complete the course exercises. During the course exercises, I was learning a fair amount, but not as much as I learned in the labs. This is to be expected however, as the main part of the course revolves around use of the labs. Personally, I really enjoyed the Buffer Overflow section and how it was presented in the videos and the manual. All-in-all, it took me about a week to go through the course materials.

<h1 style="text-align: center;">
  Lab
</h1>

After the course materials was the lab. There are a ton of systems in the lab that range from easy to insanely hard. The &#8216;insanely hard&#8217; machines were not terribly difficult, just very time consuming. My strategy in the beginning was to go for all the low hanging fruit, and move onto another machine when I could not figure out what to do, and circle back to it later. During this process I used Metasploit as little as possible, relying on exploits off of [exploit-db](https://www.exploit-db.com/) and [securityfocus](http://www.securityfocus.com/). Personally, I would avoid any other site hosting exploits because they are not as reputable and reliable as these two sites (unless you are using Metasploit of course). Eventually, after picking apart servers for a good couple weeks, I had narrowed down my choices to harder ones. This process took about 2-3 weeks for me to get 20 servers compromised. At this point I had to change my strategy as the servers leftover were more difficult than the others.

<h1 style="text-align: center;">
  Burnout
</h1>

During this time my pace slowed down. I was burnt out on compromising around the first half of servers in a relatively short time (I had used 1 month of my 3 months by that time!). However, I would still manage to get root/SYSTEM about 2 or 3 times a week during this period. Eventually, even these started to wind down to less and less. This is mainly due to my poor post-exploitation in the labs. When I started, I had not realized that many servers are connected to each other. Always check for password files and connections to other computers after you compromise! This would have saved me a bunch of time. However, I do believe that the correlations between servers can be a bit cryptic, so its best if you look in the forums for the hostname or IP and see if you can get any more help if you are completely stuck.

By the end of this stage I was around 2 months of lab time and only had about 10 servers left, including all networks. I had compromised servers in all the departments (including admin) and began to look at my list of servers leftover. I know a lot of them had dependencies on other servers which I thought was a huge pain at the time because I had been pretty burnt out at this point. On a whim, I decided that I would attempt the certification exam and see what would happen. After all, the retake for the certification is very cheap.

<h1 style="text-align: center;">
  Exam
</h1>

I was going into the first week of my 2nd month (still had 30 days lab time left) and took the exam. During the first couple hours of the exam I was panicking, I had not compromised any servers and barely thought that I was making process. However, I decided that I would do the exploit that I knew I had experience in and could accomplish. Doing so took about 1-2 hours to complete the full compromise of the system and I had gained 25 points out of the necessary 70 to pass. At this time, I was feeling a bit better and decided to go back and enumerate what I had collected from the beginning phases of the exam to see what I could exploit. This led me to some exploits which landed me a shell on the server. I thought that the server was vulnerable to a privilege escalation exploit but did not work when I ran it the first time. I searched around on the server about half an hour before deciding to attempt to re-run the exploit again, which landed me a root shell. The reason it didn&#8217;t work on the first attempt is because my shell had low interactivity and did not run the exploit properly. This machine was worth about 20 points so I had 45 points. By this time I was about 6-7 hours into the exam and started feeling a little better. The next server I tackled was seemingly difficult, so I opted to use my 1 Metasploit attack on this server. This proved useful because I had a low level shell with a built in metasploit module. This too, took about 2-3 hours to find the vulnerability. The next step was escalating, I tried many methods but none of them proved fruitful, eventually I decided that I would just move on and try other servers.

Looking at the other servers I noticed an application that I knew was exploitable and ran a quick exploit against it, instant low level shell. Then from the labs, I knew the exact privilege escalation that I needed to run and uploaded it to the server and ran it. Instant root shell. This machine in total took about 15 minutes (no joke!) and was worth 20 points as well. At this point I had 65 points and a low privilege shell. I determined that this was probably enough but was not sure how exactly the low level shell would affect the grade since I needed 70 points to pass. Instead I decided to exploit the other remaining server that I did not have a shell on. I was disappointed because I had spent about 3-4 hours on this last remaining machine, which was worth the lowest amount of points. That&#8217;s right, the most amount of time on the exam was spent on the (what I assume to be) the easiest machine! That was disheartening but I pushed through it and missed an essential step of my enumeration. Finding the exploit for what I found was easy enough so I attempted it and got a root shell.

At this point I was probably 13-14 hours into the exam and had 75 points from roots and another low privilege shell on a high point machine. I decided that this was good enough since I had run plenty of privilege escalation attacks on the server but was not getting anything back. Satisfied with myself, I decided to finish it all in the day and write the documentation directly after. In all, the exam took me 16 hours to complete fully, or 14 hours to exploit the machines with 2 hours of exam reporting. I sent Offensive Security my report and went to bed at 2 AM.

<h1 style="text-align: center;">
  Results
</h1>

The response from Offensive Security took about about a day or two. The email said that I had passed and I would get my certificate within 80 days! This was pretty exciting for me to hear and I am happy that I passed. Overall, I had learned a lot from the course and would recommend it. Personally, I did not find it nearly as difficult as people had made it out to be, but I will say that it requires a lot of dedication and ambition to keep Trying Harder. I would recommend for people who have a similar work ethic as mine (in the beginning 2-3 hours a day and 5-6 hours on weekends, to 1-2 hours a day and 3-4 hours on weekends in the middle) to purchase the 60 day lab. I had finished what I needed to pass in this amount of time so unless you want to get all the servers in the lab, I would say to get 60 days as a base and extend if you think you need it.

<h1 style="text-align: center;">
  Advice
</h1>

When giving recommendations to people on what they should do to prepare for the exam, many people recommend to script the enumeration process. I decided that I would not do this step since I never used it in the labs but still made good progress without it. I would simply run `nmap -A IP` and look at the results to determine next steps. Although this took up some time it was not nearly as detrimental as many others had made it out to be. I would say that you would be prepared for the exam if you made it to the Admin network and had around 10-15 machines left **TOTAL** in the labs.

Dedication and ambition is all you need to power through this course to learn the necessary skills to better yourself in offensive security.

Going through the course I would always remember: <a href="https://www.youtube.com/watch?v=co3xJRIuc6o" target="_blank">its easy to dream a dream but harder to live it</a>.