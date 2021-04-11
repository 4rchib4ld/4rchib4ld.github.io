---
title: IcedID on my neck I'm the coolest
toc: true
toc_sticky : true
excerpt: "Unpacking IcedID in order to extract the C2 domain name"
show_date: true
categories:
    - Blog
tags:
    - MalwareAnalysis
---

## Introduction

With the [takedown of Emotet with the Operation Ladybird](https://www.wired.com/story/emotet-botnet-takedown/), there is now room for a new challenger to take the throne of the "Yeah, it's me who delivers the bad stuff". This past few days I saw a new campaign of IcedID and decided to take a closer look.

The goal of this post is to unpack IcedID and recover the C2 url as quickly as possible.

## Getting our hands dirty

First, we have to find a sample. For this my go-to place is https://bazaar.abuse.ch.

The sample used during this post can be found [here](https://bazaar.abuse.ch/sample/0a0b3d91698a46d409791d4dd866e56ddd70f91a3f1d4557a0cb2899bda1e524/).

The tools we will use are :
- [PEBear](https://hshrzd.wordpress.com/pe-bear/)
- [x64dbg](https://x64dbg.com/)
- [PeStudio](https://www.winitor.com/)
- IDA (optional)

![]({{site.baseurl}}/assets/images/Pasted image 20210411115719.png)

As you may have notice, the file is a dll and not a .exe file, meaning that just running it in the debugger won't work. We need the help of `rundll32` for this. So first we got to open it with x64dbg and change the commandline to :
`"C:\Windows\System32\rundll32.exe" PathToSample\0a0b3d91698a46d409791d4dd866e56ddd70f91a3f1d4557a0cb2899bda1e524.bin, DllRegisterServer`

💡 : Rundll32.exe needs to be specified a function for running. DllRegisterServer is the function triggered in the MalDoc and is the EntryPoint of the malicious behavior. If you are using DllMain as an entrypoint, nothing will happens.
{: .notice--info}

Hitting F9 (Run) or clicking on the right arrow places us in the Rundll process

![]({{site.baseurl}}/assets/images/Pasted image 20210411082902.png)

Going to the breakpoint tab, right clicking gives this menu. You can add a "dll breakpoint", so when the debugger enter the dll, it stops

![]({{site.baseurl}}/assets/images/Pasted image 20210411083011.png)

💡 : The expected name is the same as the filename
{: .notice--info}

Executing a couple of time until we hit the entrypoint of our dll

![]({{site.baseurl}}/assets/images/Pasted image 20210411083221.png)

We can now set as many breakpoints that we want.
For unpacking this sample, only [VirtualAlloc](https://docs.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualalloc) is needed, but do not hesitate to add breakpoints on [CreateThread](https://docs.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-createthread) or [GetProcAddress](https://docs.microsoft.com/en-us/windows/win32/api/libloaderapi/nf-libloaderapi-getprocaddress) if you want to go deeper.

[VirtualAlloc](https://docs.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualalloc) will be hit 3 times by the sample :
1. Memory allocation for the payload (decrypted 2nd stage)
2. Memory allocation for data in .data (encrypted 2nd stage)
3. Memory allocation for the creation of a new thread (execution of the 2nd stage)

Upon hitting our first breakpoint on [VirtualAlloc](https://docs.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualalloc):

![]({{site.baseurl}}/assets/images/Pasted image 20210411083458.png)

Let's hit the "Execute until return" button. The value stored in RAX after this is the address of the allocated memory region. In my case it's 0x01B0000. Following it in dump :

![]({{site.baseurl}}/assets/images/Pasted image 20210411083615.png)

Now let's pretend we don't know what coming. A good way I found is to set breakpoint on the allocated region for access.

💡 : Making a breakpoint on "write" is also a good idea, but for whatever reason I didn't really work in my case
{: .notice--info}

![]({{site.baseurl}}/assets/images/Pasted image 20210411083759.png)
*right click on the memory region in dump*

Something that looks like junk is written to the first allocated region of memory

![]({{site.baseurl}}/assets/images/Pasted image 20210411084327.png)

Can you guess what will this become ?

![]({{site.baseurl}}/assets/images/peHeader.gif)

Sounds familiar isn't it ? That's actually the 2nd stage which is responsible of the C2 communication and that's where we will find the C2 config.
Now we just got to dump the memory to a file

![]({{site.baseurl}}/assets/images/Pasted image 20210411095238.png)

Opening it with PeStudio :

![]({{site.baseurl}}/assets/images/Pasted image 20210411120802.png)

All imports are resolved, no need to remap of anything

![]({{site.baseurl}}/assets/images/Pasted image 20210411095327.png)

Opening it with IDA, we only got a small set of functions 

![]({{site.baseurl}}/assets/images/Pasted image 20210411104355.png)

Nothing is obfuscated and you can quite easily find the function responsible for the C2 communication :

![]({{site.baseurl}}/assets/images/Pasted image 20210411104458.png)

You can also notice the making of the cookie that will be sent to the C2 :

![]({{site.baseurl}}/assets/images/Pasted image 20210411104621.png)

Here we are interested in the config, so let's see how this is stored and decrypted.
First it loads the address of an array located in the .data section. Then the array is decrypted in a for loop with a xor.
Translating this in python gives :
```python
decrypted = ""
 for i in range(32):
 	decrypted += chr(payload[i+64] ^ payload[i])
```

I guess we are lucky because that's not that difficult. Even more simple for you, I made a script that extract the payload and decode the config, you can find it [here](https://gist.github.com/4rchib4ld/98ca1b860c301afbba63b9617d4a00d8)

You can also got the domain name easily by setting a breakpoint on "[WinHttpConnect](https://docs.microsoft.com/en-us/windows/win32/api/winhttp/nf-winhttp-winhttpconnect)" and looking at the RDX register value

![]({{site.baseurl}}/assets/images/Pasted image 20210411145910.png)

💡 : There is two call to this API, the first one is to "aws.amazon.com" in order to check if there is an internet connection (and also an anti sandbox)
{: .notice--info}


## Cleaning our hands

To be honest I wanted to have a fully automated script with [Qiling](https://github.com/qilingframework/qiling) but due to the emulation and all of the calculation done my script takes literally hours to hit the [VirtualAlloc](https://docs.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualalloc) call, so that's pretty useless.
If you got any idea on how to extract the payload quicker, do not hesitate to hit me up on [Twitter](https://twitter.com/4rchib4ld).

I didn't make a deep dive on every routine and functions of the two executable because I don't think this is really interesting as this is something pretty common and I would like my posts to give as much value as possible and not enumerating everything if it doesn't help in our mission.

With this, you can extract the C2 domain in less than 3 minutes, which is not that bad no ?

![]({{site.baseurl}}/assets/images/penguins_by_dragonith_ddqf6ac-350t.jpg)

As always, thanks for taking the time to read this, hope you learned something ! 😇


