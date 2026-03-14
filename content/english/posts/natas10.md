---
title: "Over the wire - Natas10"
date: 2026-03-14T10:11:00+00:00
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


This one is pretty cool. Usual start followed by looking at the source code.

```bash
URL=http://natas10.natas.labs.overthewire.org

curl --user  natas10:$(cat natas10 ) $URL
```

