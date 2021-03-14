---
title: Homograph Attack Using Unicode
toc: true
toc_sticky : true
excerpt: "How Homograph attacks are done using Unicode"
show_date: true
categories:
  - Blog

---

## Introduction

This attack consist of tricking the user with a domain that look legit, like **goog1e.com**. It's old and basically everyone has heard about it "Be careful on what you click..." and such recommendations, even my parents are aware of it. A new way of using this technique has emerged : **Homograph attacks based on encoding**.

## New... and old

Using [Punycode](https://en.wikipedia.org/wiki/Punycode), it's easy to register **xn–pple-43d.com** which correspond to **apple.com**. End user will not be able to see the difference between the two, and will believe they are going to the legitimate Apple Website. You can have fun with [this tool](https://www.irongeek.com/homoglyph-attack-generator.php) for example if you want to test this. This technique is well suited for phishing, as well as [credit card skimming](https://www.bleepingcomputer.com/news/security/hackers-abuse-lookalike-domains-and-favicons-for-credit-card-theft/).

### Ascii Characters

This attack is due to the font used by web browsers, where different ASCII characters will have the same font assigned. Can you see the difference between 'р' and 'p' ? I guess no, the first one is the [Cyrillic Small Letter Er](https://www.codetable.net/decimal/1088), and the second a regular 'p'.

### Non Ascii Characters

The trouble doesn't end here. As you can use ASCII characters, you can also use non Ascii characters. This can take the form of **https://apple.com∕freeIphone12.com** . 

Can you guess where the non ASCII characters is ? 

Yeah, this is the '∕' after the `apple.com`. Turns out this is a [mathematical operator](https://www.compart.com/fr/unicode/U+2215), not a slash.

### Mitigation

This attack targets the web browsers, and some took care of it. I wasn't able to use this technique on Chrome (up to date) and Microsoft Edge. But in Firefox it worked just fine.

*Chrome warning*
![](../../assets/images/Pasted%20image%2020201219094017.png)

*Edge not working*
![](../../assets/images/Pasted image 20201219093810.png)

*Firefox OK*
![](../../assets/images/Pasted%20image%2020201219093748.png)

You can read more about [Chrome IDN policiy here](https://chromium.googlesource.com/chromium/src/+/master/docs/idn.md)

Firefox stated that's a domain registrars issue, not them [here](https://bugzilla.mozilla.org/show_bug.cgi?id=1332714)
> Our IDN threat model specifically excludes whole-script homographs, because they can't be detected programmatically and our "TLD whitelist" approach didn't scale in the face of a large number of new TLDs. If you are buying a domain in a registry which does not have proper anti-spoofing protections (like .com), it is sadly the responsibility of domain owners to check for whole-script homographs and register them.

But you can add protection by setting `network.IDN_show_punycode` to true in`about:config`.

I think there is also a way to monitor and have alerts on this issue by using a regex in your SIEM (If you got the URI in your logs of course) : `^https?:\/\/(.*)xn--(.*)`. This should cover every possible ways of I am aware of.

## References

- [Hackers Abuse Lookalike Domains and Favicons for Credit Card Theft](https://www.bleepingcomputer.com/news/security/hackers-abuse-lookalike-domains-and-favicons-for-credit-card-theft/)

- [Out of character: Homograph attacks explained](https://blog.malwarebytes.com/101/2017/10/out-of-character-homograph-attacks-explained/)

- [Phishing with Unicode Domains](https://www.xudongz.com/blog/2017/idn-phishing/)