---
title: Your attribution appears to have been applied to your life
toc: true
toc_sticky : true
excerpt: "To what point is attribution useful ?"
show_date: true
categories:
    - Blog
tags:
    - ThreatIntelligence
---

Attribution is a hot topic on the cybersecurity field. Every time a new large scale attack occurs, there is speculation towards who is responsible. Whereas attribution can be done to an Intrusion Set, True Attribution stands for connecting the Intrusion Set to real persona/state/government. Some current examples are [HAFNIUM](https://www.microsoft.com/security/blog/2021/03/02/hafnium-targeting-exchange-servers/) and [SolarWinds](https://www.cyberscoop.com/cozy-bear-apt29-solarwinds-russia-persistent/).

The debate remains open towards the actual use of attribution. Some people will say that this is useless, and others will say that it matters.

The goal of this article isn't to say who is right or not, but to reflect on the actual benefits of attribution.

## Attribution is an assessment not a declaration

Attribution is really hard to prove, first because Threat Actors don't want to be known and recognized, so they will cover their tracks the best they can, as well as providing deceptive information.

💡 : Despite the possibility for deceptive information, Threat Actors prefers to stay under the radar than giving false flags
{: .notice--info}

Second, even if an attack is proven to be from say, Russia, how can this be tied up with the actual government. ???

Thinking with [Heisenberg’s Uncertainty Principle](https://en.wikipedia.org/wiki/Uncertainty_principle), as well as [Gödel’s Incompleteness Theorems](https://www.quantamagazine.org/how-godels-incompleteness-theorems-work-20200714/), without **HARD** evidence, Attribution must be an assessment, based on attributes that can (and must) be reexamined in the future and **NEVER BE** a declaration.  
This is worth noting that Attribution is sometimes based on other attributions done by other teams, that also might be wrong, that's what analyst should be aware of the source of their knowledge and acknowledge where their analysis depends on others.


## TTPs are better than attribution on the tactical level

When doing an Incident Response, is this really needed to know that China is behind the attack ?
I would argue that on this level, the attribution forces the mind to focus only on the said Actor (APT X). The mind might become closed (the [2nd Law Of Thermodynamics](https://www.livescience.com/50941-second-law-thermodynamics.html) won't agree) and drowning with the [Confirmation Bias](https://www.verywellmind.com/what-is-a-confirmation-bias-2795024). 
There is not much interest to know that the attack was done by a PLA Unit, it won't help the ongoing incident response. The only thing that can be helping is the TTPs, which can be attributed to an actor, that enable the analysts to see what can approximately happen in the network.

However, this **MUST NOT** be the only source of thinking, just part of it. No details should be avoided, and every path must be taken. As we saw earlier, Attribution might even be wrong, so your thinking can also be wrong, leading to a wrong analysis.

Moreover, steps for mitigation and remediation are the same, whoever is behind the attack. No real time should be given to the Attribution on this level because you don't see the full scope of the attack.

## Attribution can help to see the bigger picture

When doing Attribution and creating one (or more) Intrusion Set, it enables to track an adversary as well as gaining an understanding about their motivations, means and so on.  
By giving a name, it's easier to reference and share with other peoples as well as stating the actual overlap (or not) with other groups.
I know, it's sometimes hard to understand what is going on with all of these names. However, there is a good reason for this myriad of name to exist : not everyone sees the same things and putting look alike groups into the same big box won't do any good.
Short story short : Groups naming is different because of methodologies and overlaps. 

💡 : If you want to see more on this subject, I recommend @likethecoins [video](https://www.youtube.com/watch?v=ff1yhdIx0yY) on the subject
{: .notice--info}

A recent example is [FIN7](https://attack.mitre.org/groups/G0046/) and [Carbarnak](https://attack.mitre.org/groups/G0008/), who were believed to be the same actors but actually aren't.

Attribution can link events that looked not related, but actually are. Enabling to see the bigger picture, find a motive or common TTPs.
The best example that comes to my mind is the [Witchcoven campaign](https://www2.fireeye.com/rs/848-DID-242/images/rpt-witchcoven.pdf) in which several websites were compromised by the same Threat Actor (Snake) in order to reach a targeted audience and delivering exploits.

Attribution also tries to answer the question : **why** does the attack occurred ?

For financially motivated actors, most of the time the answer will be : because they could, and you got enough money for them to consider you.  
For espionage and/or state related groups, there is a plan : You got information they want, like [China with the 5 years plan](https://www.ft.com/content/40dc895a-92c6-11e5-94e6-c5413829caa5), or you are pressuring Russia with a business deal, and they fight back.

Correlating the supposed purpose and the _[cui bono](https://en.wikipedia.org/wiki/Cui_bono)_ may assist for the attribution.

Knowing who might come after your organization can help you focus on the prioritization of threats and implements the counter measures you need the most.

## Without attribution, it will never stop

Let's say attribution is only done to abstract name associated with *only* Intrusion Sets. No country, no government, no individuals. Let's say we never care who did an attack. 
What a beautiful word for a Threat Actor, they can do whatever they want, without being held accountable for their actions. 

In reality, Attribution is the first step toward an end. 
**Don't expect ransomware to stop their activity when they are [doing millions of dollars a year.](https://www.zdnet.com/article/ransomware-gangs-made-at-least-350-million-in-2020/)**

To stop this activity, we must identify the ones behind it.

Attribution needs to be top-notch, so the attackers can't possibly deny it and sanctions against them are taken.  
Cyber attacks can be denied if no hard proof is provided. 
A good example is the China - US relationship in this matter :
USA accused China of Cyberattacks, and they [were denied because they had no hard proof that China simply couldn't ignore.](https://web.archive.org/web/20170722105458/https://www.washingtonpost.com/business/technology/chinese-hackers-suspected-in-attack-on-the-posts-computers/2013/02/01/d5a44fde-6cb1-11e2-bd36-c0fe61a205f6_story.html) Once they got one, [Cyber sanctions was ready and China accepted to stop cyberattacks against the US](https://web.archive.org/web/20170718181028/http://edition.cnn.com/2015/08/31/politics/china-sanctions-cybersecurity-president-obama/)... [For a while](https://www.nbcnews.com/tech/security/china-another-hack-us-cybersecurity-issues-mount-rcna744)...

Attribution can also be used to hurt one of the biggest cybercriminal groups  and [sending them to jail for what they did.](https://www.cyberscoop.com/fedir-hladyr-fin7-sentencing-prison/)

# Conclusion

Attribution is not only useful, it's necessary. Without attribution, the cyberspace would be the Wild West, you could hack anyone without fearing any consequences.
But do you always need to know who is being the attack to mitigate it ? I would argue that it's best to have it in a corner of your head, but keep an open mind about what you see.