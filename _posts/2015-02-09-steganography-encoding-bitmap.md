---
id: 57
title: Steganography + Encoding + Bitmap
date: 2015-02-09T19:15:55+00:00
author: nwalsh
layout: post
guid: http://nwalsh.me/?p=57
permalink: /?p=57
categories:
  - CTF
  - Security
tags:
  - CTF
  - Python
  - Steganography
---
<ins datetime="2015-02-09T19:37:00+00:00"></ins>The next anomaly I will finish is also a steganography anomaly. We are given a bitmap picture that looks like this: [<img class="alignnone size-full wp-image-59" src="https://nwalsh.me/wp-content/uploads/2015/02/pic.png" alt="pic" width="33" height="33" />](http://nwalsh.me/wp-content/uploads/2015/02/pic.png)

As you can see there isn&#8217;t a pretty picture like some of the other steganography anomalies. We can deduce that this _most likely _wasn&#8217;t made with a tool like `steghide` as you must supply a picture to hide the data in. However, it is possible that the picture supplied was a random assortment of RGB values, but `steghide` only works with JPEGs and this file is a bitmap. So lets view the RGB values of the bitmap manually.

To do so I will be using the [Pillow](https://pypi.python.org/pypi/Pillow/) module for Python. This will allow us to programmatically  get all RGB values in the picture. Since the picture is 33&#215;33 we have our dimensions that we need to get all RGB values. Here is the Python (2.7.3) script that I used to get all RGB values for the bitmap.

<pre>from PIL import Image

image = Image.open("pic.bmp")
rgb_image = image.convert("RGB")
for x in range(0,33):
    for y in range(0, 33):
        print rgb_image.getpixel((y,x))</pre>

We will now see the output in our console. Here is a snippet:
  
[<img class="alignnone size-medium wp-image-65" src="https://nwalsh.me/wp-content/uploads/2015/02/rgb-39x300.png" alt="rgb" width="39" height="300" />](http://nwalsh.me/wp-content/uploads/2015/02/rgb.png)

We can see a bunch of values and near the end RGB values with 0s which looks like padding for the image to make it 33&#215;33.

Upon inspecting the RGB values we can see that many of them are over 100 which gives the image its dark coloring. The smaller numbers are mostly 32s and 44s.

With all of these values over 100 we can assume that is significant in coming to a result. In fact, these values would all be proper ASCII for letters. I believe the best way to tell this is the amount of the number &#8217;32&#8217; which is the ASCII character for space. An ASCII chart can be found [here](http://www.asciitable.com/) to help decode the image. Let&#8217;s write our script to convert these tuples to ASCII and see if we can get anything coherent. Here is the script that I came up with:

<pre>from PIL import Image

image = Image.open("pic.bmp")
rgb_image = image.convert('RGB')
output = ""
for x in range(0,33):
    for y in range(0, 33):
        r, g, b = rgb_image.getpixel((y,x))
        output += chr(r) + chr(g) + chr(b)

print output
</pre>

You can see a few changes, instead of printing the RGB values I will assign them to variables so that I will be able to convert those to a letter. That is what the chr() function will be doing, converting all numbers into ASCII text. Let&#8217;s take a look at the output:
  
[<img class="alignnone size-medium wp-image-66" src="https://nwalsh.me/wp-content/uploads/2015/02/lorem_2-300x163.png" alt="lorem_2" width="300" height="163" srcset="https://nwalsh.me/wp-content/uploads/2015/02/lorem_2-300x163.png 300w, https://nwalsh.me/wp-content/uploads/2015/02/lorem_2-624x339.png 624w, https://nwalsh.me/wp-content/uploads/2015/02/lorem_2.png 939w" sizes="(max-width: 300px) 100vw, 300px" />](http://nwalsh.me/wp-content/uploads/2015/02/lorem_2.png)

Aha! Looks like another lorem ipsum encoding, the anomaly is finished!