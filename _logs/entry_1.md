---
layout: post
title: "Entry 1"
date: 2021-12-02
permalink: /logs/entry-1 
---
a few thoughts, about project zero's last blog post {% sidenote 1 '[This shouldn't have happened: A vulnerability postmortem](https://googleprojectzero.blogspot.com/2021/12/this-shouldnt-have-happened.html){:target="_blank"}{:rel="noopener noreferrer"}' %}:
well, imho this is most understandable project zero writeup so far :D and it is really well explained by [@taviso](https://twitter.com/taviso). actually writeup, in line with the results of the vulnerability, it scrutinize on fuzzer which couldn't find this 7 years old _memcpy_ bug. 

without further ado, mentioned bug it's called "BigSig" that found in NSS {% sidenote 2 '[Network Security Services](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSS/Overview){:target="_blank"}{:rel="noopener noreferrer"} is Mozilla's widely used, cross-platform cryptography library.' %}. Simply it caused from the maximum size signature, so untrusted signature is simply copied into this fixed-sized buffer, overwriting adjacent members with arbitrary attacker-controlled data. The signature size must match the size of the key, but there are no other limitations. The result is turns out memory corruption. 

Honestly, I'm not very interested in fuzzing, but this detailed writeup helped me learn some new cool things.


