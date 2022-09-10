---
title: It's getting hot in here
toc: true
toc_sticky: true
excerpt: "WriteUp of the satan challenge from Barbhack 2022"
show_date: true
categories:
  - CTF
tags:
  - ReverseEngineering
  - MalwareAnalysis
---


# Intro

I went to the CTF with a new laptop, and so I didn't have everything setup. It means that I didn't have a Windows VM available, so no dynamic analysis for me !
It's fine, I tend to prefer analysing stastically because it forces you to understand deeply what's going on, instead of just watching the stack/registers for a string !

I also take this opportunity to showcase how I use Ghidra to give a more detailed analysis.

So, let's analyse this little malware, shall we ?

# Analysis

> `Satan helped me to create a new malware :), finding what it does will lead you to the flag !`

As it's a CTF challenge, I didn't do the usual MD5/SHA reconnaissance, I opened it straight into Ghidra.

The beginning of the functions looks like this :

![]({{site.baseurl}}/assets/images/bb_satan_entry.png)

A lot of things is happening, but we don't really care : most of it is the CRT making various checks.

What we want to look further is `FUN_140001c00` 

![]({{site.baseurl}}/assets/images/bb_first.png)

We got two things going on :
- A function that uses the GS register and the unicode for "ntdll.dll"
- Another function that uses the return value from the first function

💡 : FS and GS are two segments registers. You can use the FS register on Windows 32-bit to access the TEB, and the GS register for Windows 64-bit.
You can read more about segment registers and why they aren't used anymore here : https://stackoverflow.com/questions/10810203/what-is-the-fs-gs-register-intended-for  
{: .notice--info}

So now that we know that the in_GS_OFFSET relates to the TEB, we just need to know what the `0x60` offset relates to. We have the answer [here](https://www.geoffchappell.com/studies/windows/km/ntoskrnl/inc/api/pebteb/teb/index.htm?tx=219) : 0x60 offset points to the PEB 

So the two parameters for the function FUN_140001000 are actually :
- unicode "ntdll.dll"
- PEB

![]({{site.baseurl}}/assets/images/2022-08-29-Barbahack2022-Satan.md.png)

With that information, it makes it easier to understand what's going on here : This functions searches for the ntdll base address by enumerating the *InMemoryOrderModuleList*.

Going back to the calling function, we understand it deeper :
![]({{site.baseurl}}/assets/images/done_entrypoint-Barbahack2022-Satan.md.png)

With that in mind, we can move forward to the second function. In it, we can see what looks like stack-strings, and a function called several times with some arguments not changing : 

![]({{site.baseurl}}/assets/images/ss_before-Barbahack2022-Satan.png)

Looking into this function, it's what we got :

![]({{site.baseurl}}/assets/images/xor_func-Barbahack2022-Satan.png)

It's a simple xor decryption routine. A little clean-up :

![]({{site.baseurl}}/assets/images/clean_xor-Barbahack2022-Satan.png)


💡 : Little tip when dealing with stackstring in Ghidra, once you know the size of the data, assign it as an array. For example, `local_60` seems to have a size of 24, as we can see in the xor_decode function. Retyping the variable to a byte[24] give an easier way to visualize :
{: .notice--info}

![]({{site.baseurl}}/assets/images/local60-Barbahack2022-Satan.png)

![]({{site.baseurl}}/assets/images/retype_var-Barbahack2022-Satan.png)

Now we can decode the variables with the xor key `0x7861786f` which gives us :
- **NtAllocateVirtualMemory**
- **NtWriteVirtualMemory**
- **NtCreateThreadEx**
- **NtWaitForSingleObject**

You might have see it, but one decryption uses a different key than the rest. Decoding it, it isn't a string... What can this be ? We will check it later.

I forgot to ask : with the API names we just decrypted and the description of the challenge, did you found what's next ? No ? Let's continue and see what happens.

The API names are used as parameters for two functions after their decryption: `FUN_14000010f0` and `FUN_140001200`

![]({{site.baseurl}}/assets/images/table_entry_Barbahack2022-Satan.png)
`FUN_14000010f0`

We can see here that `FUN_14000010f0` looks in NTDLL Export Directory for the address of the function passed as the third parameter.

![]({{site.baseurl}}/assets/images/check_entrypoint-Barbahack2022-Satan.png)

`FUN_140001200` then looks at the retrieved address and validates that it's a syscall stub.

Wait, syscall I said ? With those APIs, what we just saw and the description, we have enough clues to understand what's going on : It's an implentation of Hell's Gate !

If you don't know about Hell's Gate, it's a technique used to bypass EDR hooks.
If you want to dig deeper into Hell's Gate and/or EDR bypass, be sure to check this [article](https://alice.climent-pommeret.red/posts/direct-syscalls-hells-halos-syswhispers2/) by [AliceCliment](https://twitter.com/AliceCliment) as everything is beautifuly explained !

Here is little drawing of the inner working of the program :

![]({{site.baseurl}}/assets/images/excalidraw-Barbahack2022-Satan.png)

Now we just need to decrypt the payload and understand what it does in order to find the flag ! 

I have done it using [this cyberchef recipe](https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')XOR(%7B'option':'Hex','string':'0x5a64767a33324c6158564d586b42'%7D,'Standard',false)&input=MHgxNzU3OWIzYjY2N2JmMDE4MTUzYWFmMzM4YWZiMjUyZGM4NjEwYzNjZDU0MmZlY2U0ZjE1NThhNDFiMzAzYWYxZDc3MzEwMjAwNDk1). The output is the shellcode we want.

Opening it in Ghidra, and manually disassemble gives this :

![]({{site.baseurl}}/assets/images/shellcode_Barbahack2022-Satan.png)

Be careful, you can't XOR decode this one in Cyberchef. To do so I used this python one liner :

`>>>bytes.fromhex(hex(0x7fb9e16be26c4d79 ^ 0x298a623990e3f1b)[2:]).decode('utf-8')[::-1]`

`'brb{HG!}'`

And here we go ! We got our flag !

## Wrapping up

I really liked this challenge. First because it wasn't a crackme like often in the Reverse category, and more interesting it's a technique I haven't reversed yet so it's pretty cool !
