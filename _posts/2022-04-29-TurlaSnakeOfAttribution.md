---
title: Turla, the Snake of Attribution
toc: true
toc_sticky: true
excerpt: "How Turla tries to avoid attribution"
show_date: true
categories:
  - Blog
tags:
  - ThreatIntelligence
---

# Turla, the Snake of Attribution

Turla (also known as [Snake and more](https://malpedia.caad.fkie.fraunhofer.de/actor/turla_group)) is one of the oldest espionnage group known so far. Activities from the Turla group are known since 2006. They are believed to be a Russian State Sponsored group, with interests in Governments, Military and Press.

What is pellicular about Turla is their capabilities and also their behavior regarding attribution. Throughout this article, we are gonna explore it in more details.

## Technical geniuses... But also morons

Turla is known for having a strong technical side. Even if they also uses open source tools (like everyone else), they also develops their own exploits and their malwares are hard to analyze, with heavy use of obfuscation and unknown windows functions.

Throughout the years, Turla campaigns revealed themselves as sophisticated.
They were able to leverage a [Microsoft Exchange Transport Agent to enable persistence on a mail server](https://www.welivesecurity.com/wp-content/uploads/2019/05/ESET-LightNeuron.pdf) with LightNeuron, used at least two zero days on the [Epic Turla campaign](https://securelist.com/the-epic-turla-operation/65545/), [bypassed driver enforcement](https://www.virusbulletin.com/virusbulletin/2014/05/anatomy-turla-exploits/) and lastly being able to do (supposedly) [MITM attack at the ISP level](https://www.botconf.eu/wp-content/uploads/2018/12/2018-m-faou-j-i-boutin-turla.pdf).
Moreover, they are creative with their TTPs, for instance with the Satellite Turla campaigns (also called MAKERSMARK), where they [hijacked satellite internet links](https://securelist.com/satellite-turla-apt-command-and-control-in-the-sky/72081/) (http://blog.passivetotal.org/snakes-in-the-satellites-on-going-turla-infrastructure/).

![]({{site.baseurl}}/assets/images/20220421174151.png)

An interesting thing about the satellite Turla campaign is a leaked Canadian Intelligence document, which explains that despite having a highly sophisticated technical side, the OPSEC used by operators seems to be lacking: [They used compromised infrastructure for personnal browsing](https://www.documentcloud.org/documents/3911739-hackers-are-humans-too-partial-redacted). They even describe the campagne as "Designed by genius, implemented by morons". This is not surprising, considering that APT ties with Intelligence Agencies is not that advanced, they don't have access to all OPSEC knowledge and processes when doing operations.

## You try to false flags

APT groups would rather avoid being detected than misdirect attribution. That's not really the case with Turla. They are stealthy, but won't hesistate to cover their tracks by impersonnate another group. We will see two campaigns attributed to Turla that showcase this.

### Made in China... Or not

In 2012, during an intrusion Turla became aware that they were detected and an IR team was looking for them. They decided to plant a false flag by downloading, installing and desinstalling a malware attributed to a Chinese APT, known as [Quarian](https://www.darkreading.com/attacks-breaches/russia-based-turla-apt-group-s-infrastructure-activity-traceable). It was even more clever than this, because the C2 that they used during this operation was also based in China, to further throw the responders on the wrong road.
However, the planted sample wasn't even configured and wasn't used in the operation. The analysts figured out that it was most likely to disturb them and that Turla had just copied this sample from elsewhere.

### Iranian Take over

The Middle East is a area of interest for both Iran and Russia. There is no surprise to see them actively operates there. However, what is unusual is the fact that Turla used two Iranian malwares for their operations. They got access to the source code of two malwares (Nautilus and Neuron) and used them as their own. What they didn't know it that neither of them was previously identified nor attributed to Iran. [They were only seen with Turla operation](https://www.ncsc.gov.uk/news/turla-group-exploits-iran-apt-to-expand-coverage-of-victims).

But it goes deeper than this. In fact it seems that Turla was able to completly overtake the Iranian infrastructure and used it for their own operations.(https://www.ncsc.gov.uk/news/turla-group-exploits-iran-apt-to-expand-coverage-of-victims)
It's the only occurence of an [APT completly taking over another one](https://www.computerweekly.com/news/252479984/turlas-use-of-iranian-infrastructure-probably-opportunistic)

## Conclusion

Turla is a really interesting threat actor by their use of capabilities and the way they play around attribution. As always with attributing incidents and campaigns to a specific threat actor, it's a tricky exercice done by security companies. It's never a declaration, but an assessment based on attributes that can (and must) be reexamined in the future.
