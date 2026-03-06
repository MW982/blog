---
title: "Over the wire - Natas0"
date: 2026-03-03T21:37:00+00:00
draft: true 
toc: false
images:
tags:
  - Over the wire
  - Natas
  - Curl
  - Linux
  - Web Security
---


Natas 0

Lately I finished the Over the wire - Bandit wargame. It teaches Unix/ Linux basics and although I dont think of myself as a beginner in the Linux world.
I still taught me a few things about the Linux system and the basics of the shell. That inspired me to play another wargame - Natas. 
Natas is about another topic of interest to me - web security. I will document my journey through this wargame.

Note: I will not be documenting the passwords for the levels. I will only be documenting the steps I took to solve the level as that is recommended by the wargame creators.

natas0

Just to spice things up a bit, I decided to only use curl to fetch the websites rather than just use the browser. This way you can use all that Unix/Linux knowledge to grep the password.


```bash
curl --user natas0:natas0 http://natas0.natas.labs.overthewire.org 
```

By looking at the HTML source code you can easily find the password for the next level - natas1.


Natas1

Well right clicking has been blocked on this level. What can I do !? Oh wait, I was using curl to begin with another easy level.


More curling (and a little bit of linux basic spells to get the password -> I store the password in a file called natas1 and cat it to the curl command)
```bash
curl --user natas1:$(cat natas1) http://natas1.natas.labs.overthewire.org
```

Once again, the password is in the HTML source code. Easy peasy.

Just to practice the sed command, I decided to use it to save the password to a file called natas2. 

```bash
curl --user natas1:$(cat natas1)  http://natas1.natas.labs.overthewire.org | grep "natas2" | sed 's/.*is //' | sed 's/ -*.//' > natas2
```

Natas2

That was the first one where it didn't get solved in the first second. I noticed that body contained an image /files/pixel.png. At first I tried file / strings / xxd to find the password hidden in the image, but that led me to a dead end.
Then I though files/ is there anything else in that directory that could be useful?  Yup, I found users.txt file with usernames:passwords and here was natas3. 

Once again, easy. 
```bash

curl --user natas2:$(cat natas2)  http://natas2.natas.labs.overthewire.org/files

# Saving natas3 to a file
curl --user natas2:$(cat natas2 )  http://natas2.natas.labs.overthewire.org/files/users.txt | grep "natas3" | sed "s/.*://" > natas3

```


Users.txt didn't contain only the natas3 password, but it also contained other usernames & passwords. I tried to use those other credentials to access the natas2 website, but that didn't work :c 
Here you can see my attempt:

```
curl --user natas2:$(cat natas2 )  http://natas2.natas.labs.overthewire.org/files/users.txt | grep -v "#" | while IFS=: read -r user pass; do curl --user "$user:$pass" http://natas2.natas.labs.overthewire.org >> hmm ;  done
```


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


Natas4




