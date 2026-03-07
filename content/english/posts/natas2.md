---
title: "Over the wire - Natas2"
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


That was the first one where it didn't get solved in the first second. I noticed that body contained an image /files/pixel.png. At first I tried file / strings / xxd to find the password hidden in the image, but that led me to a dead end.
Then I though files/ is there anything else in that directory that could be useful?  Yup, I found users.txt file with usernames:passwords and here was natas3. 

Once again, easy. 
```bash
URL=http://natas2.natas.labs.overthewire.org

curl --user natas2:$(cat natas2) $URL 

# Saving natas3 to a file
curl --user natas2:$(cat natas2 ) $URL"/files/users.txt" |
grep "natas3" | sed "s/.*://" > natas3

```


Users.txt didn't contain only the natas3 password, but it also contained other usernames & passwords. I tried to use those other credentials to access the natas2 website, but that didn't work :c 
Here you can see my attempt:

```bash
curl --user natas2:$(cat natas2 ) $URL"/files/users.txt" | 
grep -v "#" | 
while IFS=: read -r user pass; do 
  curl --user "$user:$pass" $URL >> hmm_file 
done
```






