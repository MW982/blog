---
title: "Over the wire - Natas1"
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

Well right clicking has been blocked on this level. **What can I do !?**

**Note**: I will save the passwords in a file called **natasX** where **X** is the level number and will start every level by defining a URL variable.

Oh wait, I was using curl to begin with another easy level.

```bash
URL=http://natas1.natas.labs.overthewire.org

curl --user natas1:$(cat natas1) $URL 
```

Once again, the password is easliy found in the HTML. Easy peasy.

More curling (and a little bit of linux basic spells to get the password -> I store the password in a file called natas1 and cat it to the curl command)
```bash
curl --user natas1:$(cat natas1) $URL | 
grep "natas2" | sed 's/.*is //' | sed 's/ -*.//' > natas2
```
