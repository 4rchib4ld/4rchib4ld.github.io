---
title: Tomorrow night ? Honeymoon on Ice(loader) ?
toc: true
toc_sticky : true
excerpt: "Write up about the packer used by multiple threat actors during the past few months"
show_date: true
categories:
    - Blog
tags:
    - ThreatIntelligence
    - MalwareAnalysis
---

If you read my blog post religiously, you may have spotted in the [article I made about unpacking IcedID](https://4rchib4ld.github.io/blog/IcedIDOnMyNeckImTheCoolest/) that I mentioned that I couldn't find an automated way to unpack it, and it made me sad.
And what do we do when we are sad ? Yeah, we work on Malware stuff.

## Let's get technical

I tried a couple of things, without much success. The packer uses A LOOOOOOT of `for`and `while` loop, making it almost impossible for an emulator like [Qiling](https://github.com/qilingframework/qiling) to run it. It also makes it harder to understand what is going on.
I let that sink, until one day, [@c3rb3ru5d3d53c](https://twitter.com/c3rb3ru5d3d53c) showcased her new project :

![]({{site.baseurl}}/assets/images/2021-05-14-16-34-12.png)

I found it very cool, and she gave me a lead on how to proceed with my problem :

![]({{site.baseurl}}/assets/images/2021-05-14-16-30-33.png)

I never did this kind of thing, but it was about time !
First I needed to understand exactly how the packer proceed.


💡 : Because of how the code is made, I can't really show interesting screenshots as execution is scattered in all places
{: .notice--info}

It first loads two chunks of data from its PE sections. At the time of writing, there is three possibility :
- `.rdata` and 
- `.ndata` and `.data`
- `.data` (both chunks are inside)

One chunk is actually the obfuscated/encrypted/encoded packed executable, the other one is only use during the decryption process.

Then the payload gets decoded, and decrypted using a hard-coded value (it's the first 4 bytes of `.data` divided by 512, I also refer to it as "marker" when in the 3rd variant)
Afterward, there is an obfuscation that takes place, however the code changes between samples... How to overcome this ? Despite not being the same, the code *looks* the same, meaning I can catch the pattern with a [Yara rule](https://github.com/4rchib4ld/iceloader-unpacker/blob/main/iceloader.yar)... Which I did.

![]({{site.baseurl}}/assets/images/codeObfuscatrionVariants.png)
*comparison of three code variants* \n


I get the code, and run it using [Unicorn](https://www.unicorn-engine.org/) or [Qiling](https://github.com/qilingframework/qiling).

💡 : As a principle, I always prefer when computers handle the hard work
{: .notice--info}

And here we are, at the final stage ! The second chunk of data is finally used to decrypt the packed executable.

## Only IcedID ?

When doing this research, I was focused on IcedID. To test my code on multiple samples I made a Yara Rule and a retro hunt using [Risk Mitigation](https://riskmitigation.ch/yara-scan/).

💡 : I couldn't sell my whole family just to pay for VirusTotal... Sorry about this
{: .notice--info}


Guess what I found ? Not **only** IcedID, but also Bazar, like one from [this campaign](https://pastebin.com/sCzPqLLb).

So either the packer is available for sale on some forums, or the two groups shares tools sometimes.

I couldn't really find much more info on this, or maybe there is none available. Maybe you, who are reading this, have more info on the subject ! If so, do not hesitate to hit me up on Twitter !

## Cleaning our hands, for real this time

That's it, now we can really be clean, as I made three scripts in order to automate and make your life easier :

The first one is [a python script](https://github.com/4rchib4ld/iceloader-unpacker), as simple as that. It uses Unicorn for code emulation, and decrypt the IcedID config... If the unpacked executable is an IcedID sample, of course.

The second is a [Karton Unpacker module](https://github.com/c3rb3ru5d3d53c/karton-unpacker), so you can use it in your Karton pipeline.

The last one is a [mwcfg module](https://github.com/c3rb3ru5d3d53c/mwcfg), you can decrypt an IcedID sample config with it.

## Conclusion

And here we are. From what I know, it's the only public work on Iceloader, so it's like when you find a dinosaur species : you can name it whatever you want. It's not really my idea, it came from [@c3rb3ru5d3d53c](https://twitter.com/c3rb3ru5d3d53c), but I like it. Otherwise I would have given it a Pokémon name (most likely [Regice](https://bulbapedia.bulbagarden.net/wiki/Regice_(Pok%C3%A9mon))).

Hope you liked what you just read, and also my work !
This was really fun to do, despite not making great advance every day, as I'm doing everything during my free time, so it's like 1-2hours a day :(. But overall a really cool challenge !
