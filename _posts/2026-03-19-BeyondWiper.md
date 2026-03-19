---
title: Beyond the Wiper — What Unit42's Iran Analysis Misses
toc: true
toc_sticky: true
excerpt: ""
show_date: true
categories:
  - Blog
tags:
  - attribution
  - analysis
  - iran
  - opinion
---

Unit42 published an interesting article[^1] this week about the evolution of Iranian cyber capabilities. It makes a good overview of wipers allegedly used by Iran since 2012 and their reliance on administrative tools. While Unit42 focuses on the technical progression, this article explores the strategic and geopolitical drivers behind Iran’s cyber operations.

A quick note: In the following article, I refer directly to Iran, rather than specific entities such as MOIS or IRGC. I believe that it makes the reading easier, without altering the statements. For a deeper dive into the ecosystem, I recommend to read Sekoia's overview, from which I also used for reference.[^2]

## Stuxnet: The Wake-Up Call

First, the development of Iranian cyber capabilities and their usage should be understood as a direct result of lessons learned from Stuxnet. Right from the start, Iran has been the target of two of the biggest cyber powers in the world. And Iran considers them both their arch enemies. Stuxnet tried to disrupt Iran's nuclear program, a strategic asset, potentially military in nature. Stuxnet also sent a message to every country in the world: cyber can be used to achieve kinetic objectives and it is considered fair game. The impact on Iran's thinking about cyber is unknown for sure, but what is clear is that they understood what can be done with it, and worked on it[^3]. The same can be said about other major players, mainly China.

## Cyber attack as retaliation

Iran was not trying to use cyber attacks only as a plausible deniable tool, but also as both a weapon and a message. The first reported usage of Shamoon in August 2012 against Saudi Aramco—Saudi Arabia's state owned oil company—happens right after a cyberattack on Iran's oil terminals on April 2012[^4]. There is no official statement linking the two events, but it is hard not to see a possible retaliation. The attack on Iran's oil terminals was thought to be carried out by the U.S., and a retaliation against one of their biggest allies, Saudi Arabia, on the same sector can be interpreted as a response in kind. If Iran needs more stealth, it could use it too.

Iran was—or at least tried to be—stealthy. The original article omits all espionage activities attributed to Iran, whether they are domestic or abroad. The first documented campaign is from 2007, so 5 years before the Shamoon attack. It was somehow stealthy because it did not get publicly reported until 2016[^5]. APT35, APT42, Infy, MuddyWater or Oilrig deserves mention when discussing Iran's cyber capabilities because they were developed alongside wipers and information warfare, not in isolation.

Iran can also use its wide network of proxies to at least avoid direct attribution. For instance, the BiBi wiper is reported to be used by a "Pro-Hamas" group, but Check Point Research has assessed Void Manticore, an Iran-linked group, as the actor behind BiBi[^6]. The same can be said about Handala and other persona: they first appear as legitimate Palestinian group, but turned out to be operated by Iran's Ministry of Intelligence and Security (MOIS) according to Check Point Research[^7]. Iran might even—if it is not already the case— give them tools and methodologies to carry out cyber attacks like they give them weapons and cash, making attribution even more challenging.

While visibility has played a role in some Iranian cyber operations, Unit42's framing this as a priority over stealth overlooks Iran’s parallel investments in espionage and proxy networks. The truth is more nuanced: Iran’s cyber strategy is context-dependent, balancing overt retaliation with covert influence and intelligence gathering. 


## Adaptation Over Innovation: Iran’s Cyber Pragmatism

Iran has already introduced plausible deniability in their cyber operation since at least 2007 with their first espionage campaign. So ransomware was not that much of a smokescreen. This is not to say that it was not used in that way in some occurrences. But again, it needs a bit of context.

The 2020-2022 period was a big boom for ransomware activities: infrastructures needed to adapt to COVID restrictions and the state of crypto meant that it was easier to get paid with it than it was a decade ago. Groups started to emerge, cause mayhem and gain a lot of money: more than $1 billion in payments reported in the U.S. only in the period 2020-2021[^8]. Not only cybercriminals were interested.

It was a golden opportunity for some states: you can attack the western economy, and make some money at the same time. This is not lost on countries that were under heavy sanctions from the West, mainly Iran and North Korea. Iran-aligned actors did engage in ransomware operations, and not only as a *plausible deniability* cover[^8] [^9]. Other states, like Russia and China, adopt a more hands-off approach, allowing domestic hackers to operate with minimal oversight—sometimes benefiting from their actions while avoiding direct responsibility. Just as Iran adapted to the ransomware boom, it also capitalized on the growing reliance on administrative tools.

Administrative tools and EDR solutions did not exist, or not widely used in the previous decade. Now, they are powerful solutions for any IT department handling an infrastructure. It is also a great gift for threat actors, as MDM/RMM tools literally holds the key to the kingdom. Back in the day, you had to find a way to access the network, map it, make lateral movement and privilege escalation, until you are satisfied and launch your attack with your signature wiper. That's a lot of things to do, and a lot of detection opportunities, and a good EDR might catch it.

Now, threat actors just have to find a way to get the RMM administrator account and type a small set of commands. No need for custom development, and less room for attribution. It makes detection significantly harder, as activity can blend with legitimate administrative operations. Iran evolved its cyber capabilities and interest accordingly, which shows that they are well aware of their environment. It also highlights that Iran’s cyber strategy is less about innovation than about pragmatic adaptation to constraints and opportunities. Economic constraints likely reinforced Iran’s preference for cost-effective, high-impact cyber operations.


## Conclusion

The conclusion of Unit42's article is hard to dispute, defenders should take a very close look at their identity management and the rights they give to administrative tools. If they fall in the wrong hands, it could be a catastrophe. Unit42’s warning about identity weaponization is valid and urgent. However, by focusing solely on this tactical shift, we risk losing sight of the broader strategic picture: Iran’s cyber operations are not just about tools but about adapting to achieve long-term geopolitical goals. Iran-aligned actors didn’t just adopt ransomware as a smokescreen—they leveraged it as a dual-purpose tool, blending financial gain with strategic disruption. This adaptability reflects a broader pattern: Iran’s cyber operations evolve in response to environmental opportunities, such as the rise of cryptocurrency or the proliferation of administrative tools. Understanding Iran’s cyber operations requires looking beyond tools and tactics to the strategic logic that drives their use.

**References**

[^1]: https://unit42.paloaltonetworks.com/evolution-of-iran-cyber-threats/
[^2]: https://blog.sekoia.io/iran-cyber-threat-overview/
[^3]: https://www.atlanticcouncil.org/blogs/new-atlanticist/iran-s-growing-cyber-capabilities-in-a-post-stuxnet-era/
[^4]: https://www.nytimes.com/2012/04/24/world/middleeast/iranian-oil-sites-go-offline-amid-cyberattack.html
[^5]: https://unit42.paloaltonetworks.com/prince-of-persia-infy-malware-active-in-decade-of-targeted-attacks/
[^6]: https://research.checkpoint.com/2024/bad-karma-no-justice-void-manticore-destructive-activities-in-israel/
[^7]: https://research.checkpoint.com/2026/handala-hack-unveiling-groups-modus-operandi/
[^8]: https://home.treasury.gov/news/press-releases/jy0948
[^9]: https://www.bleepingcomputer.com/news/security/n3tw0rm-ransomware-emerges-in-wave-of-cyberattacks-in-israel/