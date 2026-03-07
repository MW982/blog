---
title: "Over the wire - Natas0"
date: 2026-03-03T21:37:00+00:00
draft: false 
toc: false
images:
tags:
  - Over the wire
  - Natas
  - Curl
  - Linux
  - Web Security
---


Lately I finished the Over the wire - Bandit wargame. It teaches Unix/ Linux basics and although I dont think of myself as a beginner in the Linux world.
I still taught me a few things about the Linux system and the basics of the shell. That inspired me to play another wargame - Natas. 
Natas is about another topic of interest to me - web security. I will document my journey through this wargame.

Note: I will not be documenting the passwords for the levels. I will only be documenting the steps I took to solve the level as that is recommended by the wargame creators.


Just to spice things up a bit, I decided to only use curl to fetch the websites rather than just use the browser. This way you can use all that Unix/Linux knowledge to grep the password.


```bash
URL=http://natas0.natas.labs.overthewire.org

curl --user natas0:natas0 $URL
```

By looking at the HTML source code you can easily find the password for the next level - natas1.


