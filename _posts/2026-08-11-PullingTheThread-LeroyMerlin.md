---
title: "Pulling the Thread: APT should not usurp the identity of Leroy Merlin or there will be consequences"
toc: true
toc_sticky: true
excerpt: "An APT group thought it was a good idea to use Leroy Merlin for its C2 domain. They should think again."
show_date: true
categories:
  - Blog
tags:
  - analysis
  - Asia
classes: wide
---

## Introduction

Knowing what is going on is rather important for an intelligence analyst. In cyber intelligence, this usually means reading an unhealthy number of reports, looking for something new, something interesting, or something that makes you stop and think wait, why are they doing that?

Sometimes it is a new technique. Sometimes it is a new piece of malware. And sometimes it is a domain name.

One such domain recently caught my attention: `leroymerling[.]com`.

For readers outside France, Leroy Merlin is a very large DIY retailer. It is the sort of place where you go in for a box of screws and emerge two hours later with a power drill, three metres of shelving and a vague belief that you are now qualified to renovate a kitchen. As I get older, I find myself spending an increasing amount of time there.

So when I saw `digital.leroymerling[.]com` being used as command-and-control infrastructure, I had questions.
- Why would an attacker choose a domain so obviously close to a well-known European brand? 
- Wouldn't somebody notice? 
- Wouldn't the domain be reported and taken down?

More importantly: **what else was hiding behind it?**

There was another pleasant surprise. Following this thread eventually brought me back to a server I had written about in my previous blog post.

Sometimes things just connect.

## One store, many aisles

On July 30th 2026, Kaspersky published an analysis of `OctLurk` and `SilkLurk`, two backdoors associated with a cyber-espionage campaign targeting Central Asia[^1].

Among the infrastructure identified by Kaspersky was `digital.leroymerling[.]com`, which the company identified as an OctLurk C2. Kaspersky did not explain in the article exactly what evidence led to that assessment. I wanted to see if I could find it on my own. Spoiler: no.

Looking at passive DNS data (*thanks again, Validin*) for `digital.leroymerling[.]com`. It resolved to `94.158.247[.]6`, like a lot of `leroymerling[.]com` subdomains. At the time of writing, there was no webpage available on the domain. That does not prove that the infrastructure was attacker-controlled rather than a compromised legitimate service, but it certainly makes the former explanation more *plausible*.
Then there was another domain on the same IP: `server.privivkas[.]com`. The domain was not reachable when I checked it. The name itself, however, was worth a second look: *privivkas* means *vaccinations* in Russian.

| ![]({{site.baseurl}}/assets/images/leroymerlin/PDNS_94-158.png) |
|:--:| 
| *Screenshot of the Passive DNS data on Validin.* |

`privivkas[.]com` follows the same infrastructure pattern as `leroymerling[.]com` and several domains identified by Kaspersky:
- Tucows Domains as Registrar
- Njalla for DNS

💡 : Check the IoC table at the end for more details
{: .notice--info}

The domain have several subdomains:
- support.privivkas.com -> `45.61.165.5`/ `94.158.247.22`
- server.privivkas.com -> `94.158.247.6`
- en.privivkas.com -> `45.61.165.5`
- cloud.privivkas.com -> `45.61.165.5` / `94.158.247.22`

When looking at `45.61.165[.]5`, there is only 12 domains hosted on that IP.

| ![]({{site.baseurl}}/assets/images/leroymerlin/PDNS_45-61.png) |
|:--:| 
| *Screenshot of the Passive DNS data on Validin for `45.61.165[.]5`.* |

One of them was `blsouqs[.]com`, another domain identified by Kaspersky as an OctLurk C2. Another domain, `info.divyabhabkar[.]com`, caught my attention for a different reason. It appears to impersonate one of India's largest newspapers. It also uses Tucows as registrar and Njalla for DNS.

None of these observations alone proves common ownership. But the pattern is starting to become rather difficult to ignore. As the saying goes, *there must be a pony somewhere*.

### Changing aisle

Kaspersky also identified `download.multitoconference[.]com` as an OctLurk C2. That domain resolved to `198.46.233[.]46` from July 2025 until June 2026. During that period, three other domains resolved to the same IP and shared some interesting registration characteristics:
- `a.awssynctime[.]com`: DNS through TopDNS, registrar Internet Domain Service BS Corp, and Whois Privacy Corp
- `img.hotday[.]day`: Njalla DNS and Tucows registrar
- `update.mailssa[.]com`: DNS through TopDNS, registrar Internet Domain Service BS Corp, and Whois Privacy Corp
- `multitoconference[.]com`: DNS through TopDNS, registrar Internet Domain Service BS Corp, and Whois Privacy Corp

| ![]({{site.baseurl}}/assets/images/leroymerlin/PDNS_198-46.png) |
|:--:| 
| *Screenshot of the Passive DNS data on Validin for `198.46.233[.]46`. Not present on Validin.* |

The recurring infrastructure choices are becoming more interesting than the names themselves: One group of domains repeatedly uses Tucows + Njalla. Another repeatedly uses Internet Domain Service BS Corp + TopDNS.

That does not amount to attribution. Registrars and DNS providers are not good fingerprints. But when the same combinations keep appearing around infrastructure already identified as malicious, they become useful investigative leads.

`img.hotday[.]day` provides another connection. It appears to be related to `img.gofleadservices[.]com`, a domain hosted on `45.14.244[.]110`, an IP previously identified by JSC State Technical Service of Kazakhstan.[^2]. Both domains were registered through Tucows and use Njalla for DNS.

At this point, the store is beginning to have rather a lot of back doors.

## Anydesk, but probably not that Anydesk

`96.9.124[.]237` is another interesting stop. The IP hosted both `gycudore.kozow[.]com`, identified by Kaspersky as an OctLurk C2, and `anydesk.updateservices[.]org`. The latter name is a good example of why domain names should never be treated as proof of legitimacy: `anydesk.updateservices[.]org` sounds like something that could plausibly belong to a software-update service. Turns out it does not. The domain was also associated with Cobalt Strike activity.

| ![]({{site.baseurl}}/assets/images/leroymerlin/VT_PDNS_96-09.png) |
|:--:| 
| *Screenshot of the Passive DNS data on VirusTotal for `96.9.124[.]237`. Not available on Validin.* |

The naming is almost aggressively helpful: if you were looking for infrastructure pretending to be legitimate software, this is the sort of thing you would expect to find.

## Adding to the Asian targeting

The investigation also turns up infrastructure with a more regional flavour.

`45.83.140[.]218` hosted subdomains of `annoyingremote[.]com`, a domain identified by Kaspersky as OctLurk C2 and separately reported by the JSC State Technical Service of Kazakhstan[^2]. The IP has another interesting characteristic. Its PTR record pointed to `airnav[.]tj`, the domain of *Tajikistan's Civil Aviation Agency*, at recurring but discrete points in time. We also know that the attacker has at least some interest in Tajikistan, given that another domain identified by Kaspersky ·is `tj.tajikistandip[.]com`.

| ![]({{site.baseurl}}/assets/images/leroymerlin/PDNS_45-83.png) |
|:--:| 
| *Screenshot of the Passive DNS data on Validin for `45.83.140[.]218`.* |

The infrastructure appears to have had a plausible cover story available when needed: a name associated with a government aviation organisation in Tajikistan. I cannot establish from this alone why that PTR record was used, who controlled it, or whether it was deliberately intended as camouflage. But if you are trying to make infrastructure look less suspicious at first glance, an aviation-agency hostname is certainly more useful than `please-hack-me[.]com`.

### Afghanistan, again

And now we arrive at the part that made me sit up. Again.

Kaspersky identified `195.86.120[.]2` as an OctLurk C2. Long-time readers may recognise that IP: it appeared in my previous blog post[^3]. Kaspersky does not provide a time range for the activity on `195.86.120[.]2`, so I would be careful about claiming that the infrastructure observed by Kaspersky and the infrastructure I previously investigated were operating at exactly the same time. However, the overlap is still worth exploring.

In my previous investigation, I became interested in `195.86.120[.]2` because it hosted a certificate associated with the Afghan Ministry of Interior. The same server also hosted a domain using a self-signed TLS certificate impersonating Intel Corporation, a characteristic previously associated with ShadowPad activity[^4].

This time, I searched for other servers presenting the same moi.gov.af certificate. The certificate has serial number: `50953033068446509227176517585002501162`. A search using Modat returned several other servers presenting the same certificate. All of them were on **AS20473**:
- 139.84.174[.]243
- 149.28.159[.]237
- 64.176.47[.]5
- 45.77.247[.]13

| ![]({{site.baseurl}}/assets/images/leroymerlin/modat_moi_af.png) |
|:--:| 
| *Screenshot of the Modat results* |

That is five servers, including the original IP, using a certificate impersonating an Afghan government ministry. Five servers on the same ASN presenting the same certificate associated with a regional government's domain is a sufficiently strange combination that I would not simply file it under probably nothing. But there is more! Those servers also hosted HFS, the HTTP File Server software. Not necessarily the same version, either. For example, `149.28.159[.]237` presented an HFS file-server page:

| ![]({{site.baseurl}}/assets/images/leroymerlin/screenshot_file_server.png) |
|:--:| 
| *Screenshot of the HFS page hosted on `149.28.159[.]237`* |

Unfortunately, this is where my current tooling leaves me. I could not establish what role these servers played, whether they belonged to the same operator, or whether the certificate and HFS deployments were part of the same activity. Hopefully, someone else will be able to do it!

# Conclusion

Kaspersky's report almost certainly contains more information than what is publicly available. That is hardly surprising: commercial intelligence reports tend to leave some of the good stuff behind the customer login. Starting with one rather unfortunate attempt to impersonate a French DIY retailer led to a collection of infrastructure sharing recurring registration patterns, overlapping hosting, apparent impersonation of legitimate organisations and connections to activity targeting Central Asia.
The most amusing part, at least for me, is that this investigation eventually came back to `195.86.120[.]2`, a server from my previous post.

I wonder if Kaspersky was aware of the blogpost I made about `195.86.120[.]2`. I may have found a surprisingly good way of measuring whether anyone is reading this blog.

I suppose Leroy Merlin would call that a *project*.

---


# Indicator of Compromises

The table below contains the domains discussed in this post, along with several additional indicators taken from Kaspersky's reporting. One pattern is particularly worth keeping in mind: Tucows + Njalla and Internet Domain Service BS Corp + TopDNS recur across multiple domains that otherwise appear to have little in common. It seems like a useful thread to pull!

| Domain                      | Registrar                       | DNS                                                          | Registered | Comment                                                      |
| --------------------------- | ------------------------------- | ------------------------------------------------------------ | ---------- | ------------------------------------------------------------ |
| dns.multitoconference[.]com   | Internet Domain Service BS Corp | ns-canada.topdns.com<br>ns-uk.topdns.com<br>ns-usa.topdns.com | 2024-06-05 | OctLurk C2, reported by Kaspersky                          |
| tj.tajikistandip[.]com        | Tucows Domains Inc.             | 1-you.njalla.no<br>2-can.njalla.in<br>3-get.njalla.fo        | 2025-03-04 | OctLurk C2, reported by Kaspersky                           |
| fm01.clouddevicemetrics[.]com | Tucows Domains Inc.             | 1-you.njalla.no<br>2-can.njalla.in<br>3-get.njalla.fo        | 2024-12-20 | OctLurk C2, reported by Kaspersky                           |
| confbase.mdpsupport[.]net     | NameCheap, Inc.                 | dns1.registrar-servers.com<br>dns2.registrar-servers.com     | 2026-07-30 | OctLurk C2, reported by Kaspersky                           |
| digital.leroymerling[.]com    | Tucows Domains Inc.             | 1-you.njalla.no<br>2-can.njalla.in<br>3-get.njalla.fo        | 2024-02-20 | OctLurk C2, reported by Kaspersky                           |
| api2.annoyingremote[.]com     | Tucows Domains Inc.             | 1-you.njalla.no<br>2-can.njalla.in<br>3-get.njalla.fo        | 2024-10-16 | OctLurk C2, reported by Kaspersky                           |
| about.blsouqs[.]com           | Tucows Domains Inc.             | 1-you.njalla.no<br>2-can.njalla.in<br>3-get.njalla.fo        | 2025-03-03 | OctLurk C2, reported by Kaspersky                           |
| ssl.blsouqs[.]com             | Tucows Domains Inc.             | 1-you.njalla.no<br>2-can.njalla.in<br>3-get.njalla.fo        | 2025-03-03 | OctLurk C2, reported by Kaspersky                           |
| dns.ssentialserv[.]xyz        | NameCheap, Inc.                 | dns1.registrar-servers.com<br>dns2.registrar-servers.com     | 2026-07-30 | LurkProxy C2, reported by Kaspersky                          |
| gycudore.kozow[.]com          | Dynu Systems Incorporated       | ns0.dynu.com<br>ns1.dynu.com<br>ns3.dynu.com<br>ns4.dynu.com<br>ns5.dynu.com<br>ns6.dynu.com | 2015-10-25 | SilkLurk C2, reported by Kaspersky|
| anydesk.updateservices[.]org  | Tucows Domains Inc.             | 1-you.njalla.no<br>2-can.njalla.in<br>3-get.njalla.fo        | 2024-04-01 | Resolved to the same IP as gycudore.kozow[.]com                    |
| update.mailssa[.]com          | Internet Domain Service BS Corp | ns-canada.topdns.com<br>ns-uk.topdns.com<br>ns-usa.topdns.com | 2025-01-14 |                                                              |
| privivkas[.]com              | Tucows Domains Inc.             | 1-you.njalla.no<br>2-can.njalla.in<br>3-get.njalla.fo        | 2024-03-05 | Vaccinations in Russian                                           |
| divyabhabkar[.]com            | Tucows Domains Inc.             | 1-you.njalla.no<br>2-can.njalla.in<br>3-get.njalla.fo        | 2025-05-08 | India's Best newspaper and News App                          |
| en.ftabnews[.]com             | Tucows Domains Inc.             | 1-you.njalla.no<br>2-can.njalla.in<br>3-get.njalla.fo        | 2024-07-11 | Reported by Zscaler                                          |
| steptohumanity[.]com          | NameCheap, Inc.                 | dns101.registrar-servers.com                                 | 2024-11-11 | Suspicious domain impersonating steptohumanity[.]org, an NGO |
| a.awssynctime[.]com           | Internet Domain Service BS Corp | ns-canada.topdns.com<br>ns-uk.topdns.com<br>ns-usa.topdns.com | 2026-07-13 |                                                              |
| img.hotday[.]day              | Tucows Domains Inc              | 1-you.njalla.no<br>2-can.njalla.in<br>3-get.njalla.fo        | 2025-03-26 |                                                              |
| img.gofleadservices[.]com     | Tucows Domains Inc.             | 1-you.njalla.no<br>2-can.njalla.in<br>3-get.njalla.fo        | 2025-06-30 |                                                              |

| IP                      | Comment                                                      |
| --------------------------- | ------------------------------- |
| 139.84.174[.]243        | IP hosting moi.gov[.]af TLS certificate 50953033068446509227176517585002501162 |
| 149.28.159[.]237        | IP hosting moi.gov[.]af TLS certificate 50953033068446509227176517585002501162 | 
| 64.176.47[.]5       | IP hosting moi.gov[.]af TLS certificate 50953033068446509227176517585002501162 | 
| 45.77.247[.]13        | IP hosting moi.gov[.]af TLS certificate 50953033068446509227176517585002501162 | 
| 195.86.120[.]2        | IP hosting moi.gov[.]af TLS certificate 50953033068446509227176517585002501162 | 


# References
[^1]: https://securelist.com/octlurk-silklurk-backdoors-central-asia/120840/

[^2]: https://profitday.kz/pdf/security2025/15.pdf

[^3]: https://plausible-deniability.co/blog/PullingTheThread-ChineseEspionnage/

[^4]: https://www.orangecyberdefense.com/global/blog/cert-news/meet-nailaolocker-a-ransomware-distributed-in-europe-by-shadowpad-and-plugx-backdoors
