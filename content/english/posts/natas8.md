---
title: "Over the wire - Natas8"
date: 2026-03-09T18:37:00+00:00
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


It goes like this:


```bash
URL=http://natas8.natas.labs.overthewire.org

curl --user  natas8:$(cat natas8 ) $URL
```

We get a form & index-source.html. Let's check what's up with the index-source.html:

```bash
curl --user  natas8:$(cat natas8 ) $URL"/index-source.html" > index-source.html
```

I open the file in neovim and all I get a mess with a bunch of \&nbsp; - white spaces and \&lt; \&gt; < and > signs.
```vim
# Replace all &nbsp; with a space
:%s/\&nbsp;/ /g


# Replace all <br /> with a \r - carriage return -> newline
:%s/<br \/>;/\r/g

```
Exercise for a reader is to do the same for the < and > signs.

```html
<code><span style="color: #000000">
&lt;html&gt;
&lt;head&gt;
&lt;!-- This stuff in the header has nothing to do with the level --&gt;
&lt;link rel="stylesheet" type="text/css" href="http://natas.labs.overthewire.org/css/level.css"&gt;
&lt;link rel="stylesheet" href="http://natas.labs.overthewire.org/css/jquery-ui.css" /&gt;
&lt;link rel="stylesheet" href="http://natas.labs.overthewire.org/css/wechall.css" /&gt;
&lt;script src="http://natas.labs.overthewire.org/js/jquery-1.9.1.js"&gt;&lt;/script&gt;
&lt;script src="http://natas.labs.overthewire.org/js/jquery-ui.js"&gt;&lt;/script&gt;
&lt;script src=http://natas.labs.overthewire.org/js/wechall-data.js&gt;&lt;/script&gt;&lt;script src="http://natas.labs.overthewire.org/js/wechall.js"&gt;&lt;/script&gt;
&lt;script&gt;var wechallinfo = { "level": "natas8", "pass": "&lt;censored&gt;" };&lt;/script&gt;&lt;/head&gt;
&lt;body&gt;
&lt;h1&gt;natas8&lt;/h1&gt;
&lt;div id="content"&gt;

<span style="color: #0000BB">&lt;?

$encodedSecret </span><span style="color: #007700">= </span><span style="color: #DD0000">"3d3d516343746d4d6d6c315669563362"</span><span style="color: #007700">;

function </span><span style="color: #0000BB">encodeSecret</span><span style="color: #007700">(</span><span style="color: #0000BB">$secret</span><span style="color: #007700">) {
    return </span><span style="color: #0000BB">bin2hex</span><span style="color: #007700">(</span><span style="color: #0000BB">strrev</span><span style="color: #007700">(</span><span style="color: #0000BB">base64_encode</span><span style="color: #007700">(</span><span style="color: #0000BB">$secret</span><span style="color: #007700">)));
}

if(</span><span style="color: #0000BB">array_key_exists</span><span style="color: #007700">(</span><span style="color: #DD0000">"submit"</span><span style="color: #007700">, </span><span style="color: #0000BB">$_POST</span><span style="color: #007700">)) {
    if(</span><span style="color: #0000BB">encodeSecret</span><span style="color: #007700">(</span><span style="color: #0000BB">$_POST</span><span style="color: #007700">[</span><span style="color: #DD0000">'secret'</span><span style="color: #007700">]) == </span><span style="color: #0000BB">$encodedSecret</span><span style="color: #007700">) {
    print </span><span style="color: #DD0000">"Access granted. The password for natas9 is &lt;censored&gt;"</span><span style="color: #007700">;
    } else {
    print </span><span style="color: #DD0000">"Wrong secret"</span><span style="color: #007700">;
    }
}
</span><span style="color: #0000BB">?&gt;
</span>
&lt;form method=post&gt;
Input secret: &lt;input name=secret&gt;&lt;br&gt;
&lt;input type=submit name=submit&gt;
&lt;/form&gt;

&lt;div id="viewsource"&gt;&lt;a href="index-source.html"&gt;View sourcecode&lt;/a&gt;&lt;/div&gt;
&lt;/div&gt;
&lt;/body&gt;
&lt;/html&gt;
</span>
</code>
```
This is just enough readable to see what I need to do.

There's a secret:
```javascript
$encodedSecret = "3d3d516343746d4d6d6c315669563362"
```

The function that encoded this secret did three things:
1. bin2hex - convert binary to hex
2. strrev - reverse the string
3. base64 -  encoded it in base64 

So I do the reverse: 
```bash
echo "3d3d516343746d4d6d6c315669563362" | xxd -r -p | rev | base64 -d
```

I pass the secret to the form (and keep the streak of not using a web browser for this challange):

![Attentive readers call my bs in the comments :c](https://media3.giphy.com/media/v1.Y2lkPTc5MGI3NjExbWNmbzd3YngyY2Y5ZjF4djB3a2dudndsajJtaWVrYmtqc3FoYmJ5MCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3ELtfmA4Apkju/giphy.gif)


```bash
curl --user  natas8:$(cat natas8 ) $URL -X POST -d "secret=oubWYf2kBq&submit=Submit+Query" | grep "natas9 is" | sed "s/.*is //" > natas9
```

