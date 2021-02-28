---
title: Nice to meet you, too. My name is Ryuk.
toc: true
toc_sticky : true
excerpt: "A comprehensive Ryuk threat analysis"
show_date: true
header:
  overlay_image: /assets/images/260-2602871_death-note-ryuk-death-note-wallpaper-4k.jpg
categories:
  - Blog
tags:
  - ThreatIntelligence
  - MalwareAnalysis 

---

This work is a comprehensive analysis of the ransomware Ryuk. It will be break down into three parts :
- What are the Ryuk operator interested in ?
- What tactics and tools do they typically use ?
- How can defenders detect those tools or tactics ?

## From Netflix to your enterprise

Ryuk is a Ransomware, [responsible for a third of all ransomware attacks in 2020](https://www.securitymagazine.com/articles/93769-ryuk-ransomware-responsible-for-one-third-of-all-ransomware-attacks-in-2020). Ryuk is specifically used to target enterprise environments, and specially in the recent times Big Game Hunting -- going after bigger, more secure targets in tailored operations and potentially extract larger ransoms --.
Like almost all ransomwares, Ryuk goal is to generate as much money as possible, in the shortest amount of time. They often succeed according to the reports, because the average ransom seems to be near $1Millions, and the highest was $34Millions in 2018.

For this, Ryuk will targets huge corporations and specially Windows assets (which represents the most part of their infrastructure). Clients and servers will (sadly) go the same way, disregarding of their version.

At the time of writing, there is no way to decrypt Ryuk Encryption. That's because it's the AES/RSA combo and restoring data without the keys is impossible (unless there is a failure in the implementation).

Ryuk is under constant development, new modules and functionalities are added over time.

## How does it operates ?

Ryuk is most of the time delivered by TrickBot and more recently Bazar. (**), so with a phishing mail [(T1566)](https://attack.mitre.org/techniques/T1566).

This two malwares are [attributed to the same group](https://www.zdnet.com/article/new-bazar-backdoor-linked-to-trickbot-banking-trojan-campaigns/)
{: .notice--info}

However, it was also seen to exploit accessible Remote Desktop Services on targets [(T1021.001)](https://attack.mitre.org/techniques/T1021/001/).

Ryuk can also exploit vulnerabilities like [ZeroLogon](https://threatpost.com/ryuk-ransomware-gang-zerologon-lightning-attack/160286/) or [EternalBlue](https://www.computerweekly.com/news/252489779/Ryuk-attack-downs-private-health-provider-in-major-incident) to propagate itself into the networks [(T1210)](https://attack.mitre.org/techniques/T1210/).

There is actually some works done before Ryuk is run on the victim's infrastructure.
There is an Active Directory reconnaissance [(T1018)](https://attack.mitre.org/techniques/T1018/) done using tools like :
- Mimikatz
- PowerShell PowerSploit
- AdFind
- Bloodhound
- PsExec

Investigating the Active Directory [is also a behavior seen for TrickBot](https://www.bleepingcomputer.com/news/security/trickbot-now-steals-windows-active-directory-credentials/).
{: .notice--info}

The goal here for Ryuk is to have enough knowledge and privileges to impact as much hosts and shares as possible, as well as estimating the victims infrastructure : A big Active Directory most of the time involves a big entreprise.

There is also [instances where Cobalt Strike was used](https://www.advanced-intel.com/post/anatomy-of-attack-inside-bazarbackdoor-to-ryuk-ransomware-one-group-via-cobalt-strike), as it [sometimes comes with Bazar](https://www.bleepingcomputer.com/news/security/bazarbackdoor-trickbot-gang-s-new-stealthy-network-hacking-malware/).

Once the Active Directory has delivered all of its secrets, Ryuk is executed.

**About the delivery :** Even when Emotet is involved, it actually delivers Trickbot which itself delivers Ryuk. This reinforce the accountancy between this two and the attribution to the same group : Wizard Spider
{: .notice--info}
### Malware Analysis

I was able to find a Ryuk Sample (5e1e2920736e1c00104e24ee). I will describe points that seems interesting to have in mind when dealing with Ryuk, as well as how they can be detected/mitigated.

**About Hermes :** Ryuk is most likely built on the Hermes ransomware, which had [it source code shared on various underground forums](https://research.checkpoint.com/2018/ryuk-ransomware-targeted-campaign-break/). That's why you'll see strings that relates to Hermes.
{: .notice--info}

#### Dropping itself

Upon launching, it copies itself in the current directory with a random 7 characters length and then launch the new file with the parameters "8 Lan" (Wake On Lan)

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210221085933.png)

#### Anti everything

It then creates a thread that loop thought all running processes and services and kill those who might disturb it : like excel, backup services, virtual machine and such. The full list is in the Appendix. The goal is to make sure not file are opened in memory [(T1489)](https://attack.mitre.org/techniques/T1489/), as well as performing AntiVM action [(T1562.001)](https://attack.mitre.org/techniques/T1562/001).

- `net stop` is used for services :

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210226113746.png)

- `taskkill` is used for processes :

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210226113834.png)

**Detection opportunity** : It's highly suspicious that a process kills a high number of processes and/or services. Maybe some legit apps do it, but this behavior can be a sign of a malware trying to setup its playground.
{: .notice--primary}

It will also performs anti forensics/recovery by executing various commands ([T1059.003](https://attack.mitre.org/techniques/T1059/003) and [T1490](https://attack.mitre.org/techniques/T1490)) :

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210218091816.png)

Note the typo for the first command, there is a missing 'e' on 'delete'
{: .notice--info}

**Detection opportunity** : This kind of commands are HIGHLY suspicious, and there should be a rule on the SIEM or EDR that monitor their usages.
Also, having a lot of this commands executed in the same interval can be determined in a rule
{: .notice--primary}

#### Privilege Escalation

Ryuk will check for two privileges : 
- `SEBackupPrivilege` : In order to access to any file and bypass ACL
- `SEDebugPrivilege` : In order to execute the process injection

In order to do so, Ryuk will open it current thread and retrieve the Thread Token.

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210226090247.png)

It will then enable the wanted privilege with the function `AdjustTokenPrivileges` ([T1134](https://attack.mitre.org/techniques/T1134))

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210226090526.png)

The privileges will be used on the next steps

#### Process Injection

Ryuk injects itself into multiples processes, in order to be very fast.

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210222084700.png)

It enumerates running process ([T1057](https://attack.mitre.org/techniques/T1057))checks if the process is one of `csrss.exe`, `explorer.exe`, `lsaas.exe` or is run under NT Admin and then injects into it, using `WriteProcessMemory` and `CreateRemoteThread` ([T1055](https://attack.mitre.org/techniques/T1055)).

**Detection opportunity** : It's pretty uncommon for a process to inject itself into another, or even writing to its memory. This kind of behavior can be detected by an EDR, or by Windows Sysmon Event ID 8, for example.
{: .notice--primary}

#### Encryption

The encryption ([T1486](https://attack.mitre.org/techniques/T1486)) is done with the `CryptEncrypt` Windows API with AES_256. 

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210222082250.png)

All encrypted files have the string "HERMES" at their end. It is used as a marker to know whether the file has already been encrypted. The file itself is encrypted, so there is no deletion involves and so recovery is unlikely. A file named `RyukReadMe.html` is dropped on every encrypted directory.

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210218092446.png)

There is a bunch of directory that are whitelisted by the malware :

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210221114747.png)

As well as :
-   Chrome
-   Mozilla
-   Recycle.bin
-   Windows
-   Microsoft
-   AhnLab

All encrypted files are appended the `.RYK` file extension.

Any mounted drives ([T1547.001](https://attack.mitre.org/techniques/T1547/001))will suffer the same fate, first by having rights :

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210223203130.png)

And then the encryption :

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210223203344.png)

Ryuk will also scan the ARP table in order to find any files or folders in remote hosts. ([T1016](https://attack.mitre.org/techniques/T1016))

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210223202849.png)

#### Persistence

Create a registry key named 'svchos' on the famous `HKEY\_CURRENT\_USER\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Run` registry ([T1547.001](https://attack.mitre.org/techniques/T1547/001))with the full path of the executable using `cmd.exe` ([T1059.003](https://attack.mitre.org/techniques/T1059/003))

![]({{site.baseurl}}/assets/images/Pasted%20image%2020210221114253.png)

**Detection opportunity** : This registry is a well known location for persistence. A rule can be easily setup to monitor any suspicious addition.
{: .notice--primary}

## What's next ?

Wizard Spider seems to have built an ecosystem of malware, and still continues to develop it, with the addition to Conti, a new ransomware. Some reports seems to [indicate that Conti is a new version of Ryuk](https://www.bleepingcomputer.com/news/security/ryuk-successor-conti-ransomware-releases-data-leak-site/). Maybe that's a question I will tackle during another analysis.
For now, Ryuk is still going, and should be kept in mind.

## Appendix

### Blacklisted Process
```
- virtual
- vmcomp
- vmwp
- veeam
- backup
- Backup
- xchange
- sql
- dbeng
- sofos
- calc
- ekrn
- zoolz
- encsvc
- excel
- firefoxconfig
- infopath
- msaccess
- mspub
- mydesktop
- ocautoupds
- ocomm
- ocssd
- onenote
- oracle
- outlook
- powerpnt
- sqbcoreservice
- steam
- synctime
- tbirdconfig
- thebat
- thunderbird
- visio
- word
- xfssvccon
- tmlisten
- PccNTMon
- CNTAoSMgr
- Ntrtscan
- mbamtray
```

### Blacklisted Services
```
- vmcomp
- vmwp
- veeam
- Back
- xchange
- ackup
- acronis
- sql
- Enterprise
- Sophos
- Veeam
- AcrSch
- Antivirus
- Antivirus
- bedbg
- DCAgent
- EPSecurity
- EPUpdate
- Eraser
- EsgShKernel
- FA_Scheduler
- IISAdmin
- IMAP4
- MBAM
- Endpoint
- McShield
- task
- mfemms
- mfevtp
- mms
- MsDts
- Exchange
- ntrt
- PDVF
- POP3
- Report
- RESvc
- sacsvr
- SAVAdmin
- SamS
- SDRSVC
- SepMaster
- Monitor
- Smcinst
- SmcService
- SMTP
- SNAC
- swi
- CCSF
- TrueKey
- tmlisten
- UI0Detect
- W3S
- WRSVC
- NetMsmq
- ekrn
- EhttpSrv
- ESHASRV
- AVP
- klnagent
- wbengine
- KAVF
- mfefire
```