---
title: "Cool things about XOR"
date: 2026-03-22T12:11:00+00:00
draft: false 
toc: false
images:
tags:
  - xor 
  - bit manipulation
---

What is XOR ?
--

Xor is Exclusive OR.  
It's a logical operator, which returns true when both inputs are different.
In programming it is usually indicated by **^**. In mathematics it's **⊕** or **⊻**.  
Quick look at the truth tables should explain it. 

| a | b | a ^ b |
| :-: | :-: | :-: |
| 1 | 0 |   1 |
| 0 | 0 |   0|
| 1 | 1 |   1|
| 0 | 1 |   1 |

But how does it work with strings or numbers like 67?

**It operates on bits:**

```
100 in binary is 0b1100100
10 in binary  is 0b0001010
100 ^ 10      is 0b1101110
```

Cool things about XOR are ...
---

```

# Commutative => Order doesn't matter
a ^ b = c
b ^ a = c

# Self-inverse => applying the same XOR undoes itself
a ^ b ^ b = a 
c ^ b = a

----

a = a
a ^ a = 0
a ^ a ^ a = a
...

```

If you don't see it immediatly don't fret.  
It means if you do A XOR B you will get C, and then if you do C XOR A you get the B.  
Meaning if you have a secret and a key you could XOR them together to encrypt the secret and then if you want to retrieve the secret you just do the Encrypted Secret XOR key and you get the secret.  
This way of encrypting data is known as [XOR cipher](https://en.wikipedia.org/wiki/XOR_cipher#Use_and_security). 

List of numbers with pairs problem
---

You have a huge list of numbers and you know that all of them except one occur an even number of times.
```python

numbers = [1, 2, 3, 5, 6, 3, 1, 3, 3, 2, 5, 9, 9]

# What will happen if you XOR every number in the list?
result = 0
for num in numbers:
  result ^= num

```

If you remembered the self-inverse and commutative properties of XOR, then you know that the result will be 6 as it's the only number that appears once in the list.

Swapping variables
---

Ever wondered how can you swap two variables without a third temporary one?  
Me neither, but you can do that with XOR!

```python
a = 1
b = 2

a = a ^ b
b = b ^ a
a = a ^ a
```
