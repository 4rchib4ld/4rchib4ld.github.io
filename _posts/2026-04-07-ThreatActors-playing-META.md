---
title: Threat Actors are playing the META
toc: false
toc_sticky: false
excerpt: "Cybercriminals and nation-state actors are converging on the same TTPs—not because they collaborate, but because efficiency is universal. They're all playing the META."
show_date: true
categories:
  - Blog
tags:
  - analysis
  - opinion
---

💡 : Originally published February 2025 on another platform. Lightly revised for clarity.
{: .notice--info}

On January 2025, Trellix published an [article](https://www.trellix.com/blogs/research/blurring-the-lines-how-nation-states-and-cybercriminals-are-becoming-alike/) named “Blurring the Lines”. It discuss the fact that it is increasingly difficult to tell cyber-crime and APT groups from each other with TTPs. Even if the title of the article promised to explain "how", I did not really get it. So I wrote what I think is how threat actors all use the same TTPs: they just do what is most likely to work.

Information systems are still vulnerable. threat actors want to be the most efficient possible when exploiting them. In competitive video games, this is what is called the *Most Effective Tactics Available* (META). In the context of cyber threats, it is all the Tactics, Techniques and Procedures (TTPs) that are the most likely to achieve the threat actor objective without detection and at the lowest possible cost. The META is dynamic. The threat actors learn and adapt to the cyber ecosystem, just as defenders do. If a tactic or technique is too well countered by the defenders, threat actors will change accordingly. For instance, macro-embedded documents were the go-to for initial access. Microsoft finally decided that it was time to consider this seriously, so it is a technique that belongs almost to the past. I still encounter some macro-related documents once in a while. These documents exploit old vulnerabilities like CVE-2017-11882. Some people did not install the patch, even today. 

We tend to forget, but threat actors are like penetration teams[^china]. They have at their disposal the sharing of knowledge from the offensive cyber community, especially their tooling. These tools are as efficient as custom-made tools—sometimes even better—and they are free. Because anyone can use them, attributing the usage to a particular group is not possible. Some of these tools are well established in the META.

The META also targets the insecurity by default of information systems and the difficulty to monitor them. Defense evasion techniques exploit legitimate tools and functionalities of the operating system. Living of the Land  binaries are first used by the operating system, applications, and administrators. Windows is designed to allow process injection and loading DLLs. Command and control infrastructures are similar. Some legitimate services offer the same functionalities, like uploading files and sending data. Detecting the nefarious intent behind those behaviors on a large information system is a big challenge. 

For threat actors, what has been spared in R&D costs is paid by reduced chances of success against defenders aware of the META. This is a risk cybercriminals—especially the least advanced—are more willing to take. They can always find a new target. For groups affiliated with governments and the military, they might prefer to have every chance on their side, that is if the target is worth the effort. They will more likely use their own tools and infrastructure to avoid detection.

Not every corporation can—let alone be willing to—watch over each PowerShell script execution, or block access to Dropbox. The amount of doors that can be opened by attackers is massive, and they only need one to be successful. As every group is using it, implementing defenses against the META can mutualize the effort[^defendersTools] and keep your organization safer against a lot of threats. The META is not a failure of attribution—it is a reminder that efficiency is universal. Nation-state actors and cybercriminals converge not because they collaborate, but because they operate in the same ecosystem and respond to the same incentives. Defenders who understand this stop chasing group labels and start focusing on behaviors. That is where they can deal with threat actors the best.


[^china]: In China, some threat actors are literally cybersecurity firms—for example I-Soon (FishMonger) and Chengdu 404 (APT41).
[^defendersTools]: Common offensive tools are often used as benchmarks when evaluating security products or services. A SOC team or an EDR not able to detect them is at least a waste of money.