---
layout: post
title: "Entry 1"
date: 2021-12-02
permalink: /logs/entry-1 
---
{% newthought 'a few thoughts' %} about project zero's last blog post{% sidenote 1 "[This shouldn't have happened: A vulnerability postmortem](https://googleprojectzero.blogspot.com/2021/12/this-shouldnt-have-happened.html){:target='_blank'}{:rel='noopener noreferrer'}" %}

well, imho this is most understandable project zero writeup so far :) and it is really well explained by [@taviso](https://twitter.com/taviso). actually writeup, in line with the results of the vulnerability, it scrutinize on fuzzer which couldn't find this 7 years old _memcpy_ bug. 

without further ado, mentioned bug it's called "BigSig" that found in NSS{% sidenote 2 "[Network Security Services](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSS/Overview){:target='_blank'}{:rel='noopener noreferrer'} is Mozilla's widely used, cross-platform cryptography library." %}. Simply it caused from the maximum size signature, so untrusted signature is simply copied into this fixed-sized buffer, overwriting adjacent members with arbitrary attacker-controlled data. The signature size must match the size of the key, but there are no other limitations. The result is turns out memory corruption. 

{% newthought 'keypoints' %}

- Raise the maximum size of ASN.1{% sidenote 3 "[Abstract Syntax Notation One](https://wiki2.org/en/ASN.1){:target='_blank'}{:rel='noopener noreferrer'}" %} objects produced by libFuzzer from 10,000 to 224-1 = 16,777,215  bytes. This is important point sometimes fuzzer's arbitrary limit is not setted enough for fuzzed input.
- The QuickDER fuzzer should call some relevant APIs with any objects successfully created before destroying them.
- The oss-fuzz code coverage metrics should be divided by fuzzer, not by project.

honestly, I'm not very interested in fuzzing, but this detailed writeup help me learn some new cool things.