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

I was wondering what that Refresh page was about, so I decied to use the browser to check it out. 
After clicking on the link, I got a message saying that, indicating that 

``` 
Access disallowed. You are visiting from "http://natas4.natas.labs.overthewire.org/" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/"

<br/>
<div id="viewsource"><a href="index.php">Refresh page</a></div>
```

That gave me the hint that I needed. I was supposed to use the Referer header to indicate that I was coming from natas5.
Proceeded to extract the password as usual.

```bash
URL_N5=http://natas5.natas.labs.overthewire.org

curl --user natas4:$(cat natas4 ) -H "Referer: "$URL_N5 $URL |
grep "natas5" | sed "s/.*is //"  > natas5
```

Natas5

The hint indicated that it was about authentication. It said that I wasn't logged in.
Two things instatnly come to mind when I hear the word authentication.

1. JWT - Json Web token - stateless way of authentication. The Server/Client flow goes like this:
- Client logs in with credentials
- Server verifies credentials against the DB
- Server generates a JWT using header + payload + signature (signed with secret key). JWT is a base64 encoded string, so anyone can decode it, but it is signed so no one can tamper with it. 
- Server sends the token to the client (cookie or response body)
- Client stores the token and sends it with every future request
- Server verifies the signature and if valid, reads the payload directly — no DB lookup needed — and processes the request

2. Sessions - Stateful way of authentication. It means that the server has to keep the user information usually in a database. The Server/Client flow goes like this:
- Client logs in with credentials
- Server verifies credentials
- Server generates a session ID
- Server stores session_id → user_id in a database
- Server sends session ID in a cookie to the client
- Client sends future requests with the session ID cookie
- Server looks up the session ID → finds the user → processes the request
- Server responds with the requested resource/data

What kind of authentication this challengs is using? I used curl to fetch the cookies and noticed there's a loggedin cookie with a value of 0.
I changed the value to 1 and attached the cookie to the request, and the rest is history. Used grep / sed to extract the password. On to the next one!

```bash
URL=http://natas5.natas.labs.overthewire.org

curl --user  natas5:$(cat natas5 ) $URL -c cookie-jar

cat cookie-jar
# Netscape HTTP Cookie File
# https://curl.se/docs/http-cookies.html
# This file was generated by libcurl! Edit at your own risk.

natas5.natas.labs.overthewire.org       FALSE   /       FALSE   0       loggedin        0

curl --user  natas5:$(cat natas5 ) $URL -b cookie-jar

curl --user  natas5:$(cat natas5 ) $URL -b cookie-jar |
grep "natas6" | sed "s/.*is //" | sed "s/<.*//" > natas6
```

Natas6


You know the drill. 


```bash
URL=http://natas6.natas.labs.overthewire.org

curl --user  natas6:$(cat natas6 ) $URL
```

Curl the website, found weird <a> tag, curl that 

```html
<div id="viewsource"><a href="index-source.html">View sourcecode</a></div>
```

```bash

curl --user  natas6:$(cat natas6 ) $URL/index-source.html
```

As you can see this is barely readable, but a quick glance and I see 'includes/secret.inc'. There goes another curl. 
```html
<code><span style="color: #000000">
&lt;html&gt;<br />&lt;head&gt;<br />&lt;!--&nbsp;This&nbsp;stuff&nbsp;in&nbsp;the&nbsp;header&nbsp;has&nbsp;nothing&nbsp;to&nbsp;do&nbsp;with&nbsp;the&nbsp;level&nbsp;--&gt;<br />&lt;link&nbsp;rel="stylesheet"&nbsp;type="text/css"&nbsp;href="http://natas.labs.overthewire.org/css/level.css"&gt;<br />&lt;link&nbsp;rel="stylesheet"&nbsp;href="http://natas.labs.overthewire.org/css/jquery-ui.css"&nbsp;/&gt;<br />&lt;link&nbsp;rel="stylesheet"&nbsp;href="http://natas.labs.overthewire.org/css/wechall.css"&nbsp;/&gt;<br />&lt;script&nbsp;src="http://natas.labs.overthewire.org/js/jquery-1.9.1.js"&gt;&lt;/script&gt;<br />&lt;script&nbsp;src="http://natas.labs.overthewire.org/js/jquery-ui.js"&gt;&lt;/script&gt;<br />&lt;script&nbsp;src=http://natas.labs.overthewire.org/js/wechall-data.js&gt;&lt;/script&gt;&lt;script&nbsp;src="http://natas.labs.overthewire.org/js/wechall.js"&gt;&lt;/script&gt;<br />&lt;script&gt;var&nbsp;wechallinfo&nbsp;=&nbsp;{&nbsp;"level":&nbsp;"natas6",&nbsp;"pass":&nbsp;"&lt;censored&gt;"&nbsp;};&lt;/script&gt;&lt;/head&gt;<br />&lt;body&gt;<br />&lt;h1&gt;natas6&lt;/h1&gt;<br />&lt;div&nbsp;id="content"&gt;<br /><br /><span style="color: #0000BB">&lt;?<br /><br /></span><span style="color: #007700">include&nbsp;</span><span style="color: #DD0000">"includes/secret.inc"</span><span style="color: #007700">;<br /><br />&nbsp;&nbsp;&nbsp;&nbsp;if(</span><span style="color: #0000BB">array_key_exists</span><span style="color: #007700">(</span><span style="color: #DD0000">"submit"</span><span style="color: #007700">,&nbsp;</span><span style="color: #0000BB">$_POST</span><span style="color: #007700">))&nbsp;{<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;if(</span><span style="color: #0000BB">$secret&nbsp;</span><span style="color: #007700">==&nbsp;</span><span style="color: #0000BB">$_POST</span><span style="color: #007700">[</span><span style="color: #DD0000">'secret'</span><span style="color: #007700">])&nbsp;{<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;print&nbsp;</span><span style="color: #DD0000">"Access&nbsp;granted.&nbsp;The&nbsp;password&nbsp;for&nbsp;natas7&nbsp;is&nbsp;&lt;censored&gt;"</span><span style="color: #007700">;<br />&nbsp;&nbsp;&nbsp;&nbsp;}&nbsp;else&nbsp;{<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;print&nbsp;</span><span style="color: #DD0000">"Wrong&nbsp;secret"</span><span style="color: #007700">;<br />&nbsp;&nbsp;&nbsp;&nbsp;}<br />&nbsp;&nbsp;&nbsp;&nbsp;}<br /></span><span style="color: #0000BB">?&gt;<br /></span><br />&lt;form&nbsp;method=post&gt;<br />Input&nbsp;secret:&nbsp;&lt;input&nbsp;name=secret&gt;&lt;br&gt;<br />&lt;input&nbsp;type=submit&nbsp;name=submit&gt;<br />&lt;/form&gt;<br /><br />&lt;div&nbsp;id="viewsource"&gt;&lt;a&nbsp;href="index-source.html"&gt;View&nbsp;sourcecode&lt;/a&gt;&lt;/div&gt;<br />&lt;/div&gt;<br />&lt;/body&gt;<br />&lt;/html&gt;<br /></span>
</code>                                                    
```


```bash

curl --user  natas6:$(cat natas6 ) $URL/includes/secret.inc
```

I bet I need to POST it in the form.

```html
<?
$secret = "FOEIUWGHFEEUHOFUOIU";
?>
```

I tried to POST it, but it didn't work, so I decided to use the browser to send the POST request.

```bash
curl -X POST --user natas6:$(cat natas6 ) $URL -F "secret=FOEIUWGHFEEUHOFUOIU"
```

