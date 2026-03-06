---
title: "Over the wire - Natas3"
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

Natas3

The hint was :
<!-- No more information leaks!! Not even Google will find it this time... -->

I completely randomly tried to check the robots.txt file... 

```bash
curl --user natas3:$(cat natas3)  http://natas3.natas.labs.overthewire.org/robots.txt
```

There I found /s3cr3t/ which then led me to the /s3cr3t/users.txt file with the password for natas4.
In the hindsight I get it "Not even Google" web crawlers will find it. Robots.txt is a informational file for web crawlers to see what they should index or not...


```bash
curl --user natas3:$(cat natas3 )  http://natas3.natas.labs.overthewire.org/s3cr3t/users.txt  | grep "natas4" | sed "s/.*://" > natas4
```


