---
title: Nothing but dotnet when we shoot
toc: true
toc_sticky: true
excerpt: "Little talk about dotnet and its use in malware"
show_date: true
categories:
  - Blog
tags:
  - ReverseEngineering
  - MalwareAnalysis
---


While doing some threat hunting, I pivoted to a range of domains. Every time there was a .NET delivered by one of this domain, I already knew it was a RAT/Stealer type of malware. Because I love to ask dumb questions on the internet to understand the world better, [I asked why is that so](https://twitter.com/4rchib4ld/status/1593288968828817414). The rest of this blog post will be around the question of the development of malware in .NET.


# First of all, what is .NET ?

![]({{site.baseurl}}/assets/images/Drawing 2022-11-19 17.32.00.excalidraw.png)

- C# programs run on **.NET**, a virtual execution system called the **common language runtime (CLR)** and a set of class libraries. The CLR is the implementation by Microsoft of the **common language infrastructure (CLI)**, an international standard. The CLI is the basis for creating execution and development environments in which languages and libraries work together seamlessly.
- Source code written in C# is compiled into an [intermediate language (IL)](https://learn.microsoft.com/en-us/dotnet/standard/managed-code) that conforms to the CLI specification.
- When the C# program is executed, the assembly is loaded into the CLR. The CLR performs Just-In-Time (JIT) compilation to convert the IL code to native machine instructions. The CLR provides other services related to automatic garbage collection, exception handling, and resource management. Code that's executed by the CLR is sometimes referred to as "managed code." "Unmanaged code," is compiled into native machine language that targets a specific platform.

## More on C#
>C# is an object-oriented, _**component-oriented**_ programming language. C# provides language constructs to directly support these concepts, making C# a natural language in which to create and use software components. Since its origin, C# has added features to support new workloads and emerging software design practices. At its core, C# is an _**object-oriented**_ language. You define types and their behavior.

- Some of the cool features that C# has a program language includes :
	- **Garbage Collection** : automatically reclaims memory occupied by unreachable unused objects
	- **Nullable Types** : guard against variables that don't refer to allocated objects
	- **Exception Handling** : provides a structured and extensible approach to error detection and recovery.
	- **Lambda Expressions** : support functional programming techniques
	- **Language Integrated Query** : syntax creates a common pattern for working with data from any source
	- **Support Asynchronous Operations** : provides syntax for building distributed systems.
	- C# allows dynamic allocation of objects and in-line storage of lightweight structures. 
		- C# supports generic methods and types, which provide increased type safety and performance. C# provides iterators, which enable implementers of collection classes to define custom behaviors for client code.

That's for the managed code part. C# also allows the code to be unmanaged. You see all the features I listed above ? You can still say, screw this, I'm going to do it my own way. Unmanaged code doesn't run inside the CLR and thus are considered *unsafe*.

💡 : There is a [functionnality](https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/) called **Ahead Of Time** that compiles the code directly to native code and doesn't need the JIT compiler to run while still being managed code. It's brand new in .NET7 !
	- It only supports a limited number of libraries in .NET 7.
{: .notice--info}


# C\# in the context of malware
C\# is very popular in malware. First because it's an easy language with powerful features, as we have seen above. You can also have access to Windows functionality rather easily. With just these arguments, it's easy to imagine why Threat Actors might be interested in this language. Even more when you take into account the amount of open-source code available to do what you want (even bad things, like a RAT).

The advantage of using C# is that you can easily implement functionality and modules and focus on evasion. To do so, Threat Actors use packing and/or obfuscation. For example, [AgentTesla](https://malpedia.caad.fkie.fraunhofer.de/details/win.agent_tesla) (of course it's a RAT) can look like this :

![]({{site.baseurl}}/assets/images/Pasted image 20221102155131.png)
You can read a cool AgentTesla Analysis [here](https://t.co/Wjx4wFxdvj) if you are interested to see packing/obfuscation in action

Moreover, Threat Actors can even leverage specific .NET functionalities.
If you did the FlareOn 9, for your greatest pleasure, you had to deal with two .NET challenges. They are both inspired by real world malwares and abuses [Mixed Mode Assemblies](https://www.mandiant.com/sites/default/files/2022-11/06-alamode.pdf) and [Dynamic Methods](https://www.mandiant.com/sites/default/files/2022-11/08-backdoor.pdf).

## .NET Reverse Engineering

As we saw, .NET malware can be hard to work with, because of the various mechanisms Threat Actors uses. But they do so because .NET by design is rather easy to analyze.
Remember the schema I did just before? We can actually decompile the generated executable with awesome tools like DnSpy.
- Just to be exact, when using .NET decompilers we see *decompiled code* based on the IL, so be aware of it


By default, .NET executable also contains a lot of metadata. My favorite one is the GUIDs. It's an awesome pivot to try, you might find APT related code well hidden on GitHub (just imagine...). You can read more about them [here](https://www.virusbulletin.com/virusbulletin/2015/06/using-net-guids-help-hunt-malware/#citation.3). VirusTotal also implement them, and you can pivot directly from there !

## Going further

If you are interested in .NET analysis, I can't recommend enough [this paper](https://media.defcon.org/DEF%20CON%2027/DEF%20CON%2027%20presentations/DEFCON-27-Alexandre-Borges-dotNET-Malware-Threats.pdf) from [Alexandre Borges](https://twitter.com/ale_sp_brazil)
There is also interesting people on Twitter :
- [@dr4k0nia](https://twitter.com/dr4k0nia)
- [@washi_dev](https://twitter.com/washi_dev)
- [@vinopaljiri](https://twitter.com/vinopaljiri)

# Conclusion
I was really surprised with the engagement of my *dumb* question, and I thought that it could be interesting to compile (pun intended) everything that was said and add some more information. Doing a blog post is easier than replying to all conversations. Let me know if you found this interesting. I know I haven't been that deep into .NET shenanigans, but others already explained everything