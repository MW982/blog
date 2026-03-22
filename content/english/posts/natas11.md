---
title: "Over the wire - Natas11"
date: 2026-03-22T12:11:00+00:00
draft: false 
toc: false
images:
tags:
  - Over the wire
  - Natas
  - Curl
  - Linux
  - Web Security
  - xor 
---


I suggest reading my other post about [XOR]({{< relref "xor.md" >}}) before going into this 
post. You will need to understand "XOR encryption" for this natas.

Usual start:
```bash
URL=http://natas11.natas.labs.overthewire.org

curl --user  natas11:$(cat natas11 ) $URL

curl --user  natas11:$(cat natas11 ) $URL"/index-source.html"
```

In this issue I found a process of loading a cookie. It does 3 things:
1. json_encode an array
2. xor_encrypt the json 
3. base64 the json and load it as the cookie 

What needs to be done?
--

I need to create a cookie, that has showpassword equal to yes, then I need to do the process described above and send this requst with a cookie.  
First problem is that I don't have the key that is used in the xor_encrypt function, but ...  
XOR encryption is pretty simple to break, when you know what is being encrypted and the output of the encrytion.  

I wrote a python script to solve this which retrieves the key of the xor encryption:
```python
cookie = "HmYkBwozJw4WNyAAFyB1VUcqOE1JZjUIBis7ABdmbU1GIjEJAyIxTRg="

array = "{\"showpassword\":\"no\",\"bgcolor\":\"#ffffff\"}"

cipher = base64.b64decode(cookie)

key = bytes([cipher[index] ^ ord(char) for index, char in enumerate(array)])
# key = b'eDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoe'

key = "eDWo"
```

Key is repeated because it's smaller than the secret which was encrypted (and we can see modulo operation in the xor_encrypt function).  
After that I took the php script, and changed the **showpassword** to yes and inputed my key into the xor_encrypt. Rerun that and I got my new cookie. 

One request later I got the password for the next level.


