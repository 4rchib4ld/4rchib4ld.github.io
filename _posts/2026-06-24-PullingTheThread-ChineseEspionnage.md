---
title: "Pulling the Thread: Two Unreported Infrastructure Clusters Linked to Chinese Espionage Tooling"
toc: true
toc_sticky: true
excerpt: "Two unreported infrastructure clusters linked to Chinese espionage tooling uncovered through infrastructure pivoting."
show_date: true
categories:
  - Blog
tags:
  - analysis
  - China
classes: wide
---


## Introduction

This post documents two infrastructure findings made during a recent client engagement. Neither cluster has appeared in public threat intelligence reporting at the time of writing. I am publishing now to maximise the defensive value of the indicators before the infrastructure rotates.

The two findings are analytically independent but share a common thread: both connect to tooling associated with Chinese state-sponsored espionage, and both extend clusters that have been partially documented by other researchers. I will walk through the pivot chain for each, state my confidence assessments explicitly, and flag where the chain is inferential versus technically anchored.

IOCs are available as a STIX 2.1 bundle on request. All indicators carry confidence scores and sourcing on every object.

💡 : The analysis cut-off for this investigation is May 28th 2026
{: .notice--info}

---

## Finding 1 — Unreported ShadowPad Infrastructure Cluster

### Detection Anchor

**ShadowPad** C2 infrastructure can be fingerprinted via a characteristic HTML body hash: `e760bb9ce1e83e274def380574509c7b9e9088ff`. Searching on this hash returns a consistent set of hosts with overlapping provider distribution and naming conventions documented by Hunt.io[^1] in their February 2024 tracking of ShadowPad via non-standard certificates.

Applying this search returned 23 hosts. Three of these—`172.64.80.1`, `104.21.96.85`, `172.67.175.133`—are Cloudflare shared infrastructure fronting the domains and are excluded from the indicator set to avoid false positives. The remaining 20 hosts had not appeared in any public reporting.

### Two Unreported Domains

Two domains in the cluster stood out immediately based on naming convention:

- `cashmicrosoft[.]com`
- `googledrivecloud[.]com`

Both follow the Microsoft and Google impersonation pattern consistently observed in ShadowPad operator infrastructure. Both are proxied behind Cloudflare. The HTML body hash match gives medium-high confidence on both as ShadowPad-linked.

**Confidence: medium-high**

### The Afghan Ministry of Interior Pivot

On 2026-05-14, `cashmicrosoft[.]com` was observed in Validin presenting a host certificate for `moi.gov[.]af`—the Afghan Ministry of Interior. Presenting a government ministry certificate is consistent with infrastructure prepared for operations involving Afghan government entities, although certificate reuse alone is insufficient to establish active targeting.

| ![]({{site.baseurl}}/assets/images/chinese_espionnage/image.webp) |
|:--:| 
| *Screenshot of the certificate on Validin. Sadly, the result is not available anymore in the Community Edition* |

I pivoted on this certificate behaviour and identified a second host exhibiting the same pattern: `195.86.120[.]2`.

**Confidence on targeting inference: medium**

### Pivot to the Intel Certificate Cluster

`195.86.120[.]2` had previously resolved a single domain for approximately two weeks: `rallyracingglobal[.]com`. No other domain has ever resolved to this IP. Investigating `rallyracingglobal[.]com` revealed a self-signed TLS certificate impersonating Intel Corporation:

```
C=US, ST=CA, L=Santa Clara, O=Intel Corporation, CN=Intel Corporation - Client Components Group
```

This certificate is observed on 9 hosts globally since February 2026. I am unaware of any legitimate Intel Corporation deployment that would expose a self-signed certificate with this subject on internet-facing infrastructure.

The tradecraft mirrors the Dell Data Vault certificate cluster documented by Hunt.io[^1] as ShadowPad infrastructure: self-signed certificates impersonating US hardware vendors on Nginx-serving hosts with characteristic ShadowPad HTTP response patterns.

The nine hosts are:

- `38.60.255[.]134`
- `38.60.208[.]77`
- `65.20.67[.]90`
- `139.180.137[.]3`
- `103.140.187[.]9`
- `103.140.186[.]81`
- `193.56.255[.]178`
- `rallyracingglobal[.]com`
- `www.rallyracingglobal[.]com`

**Confidence on Intel cert cluster as ShadowPad-related: medium**

### Infrastructure Cohesion — 38.60.x.x

The most significant observation in this dataset is the `38.60.x.x` /16 overlap across the two sub-clusters:

- `38.60.250[.]74` — present in the HTML body hash cluster
- `38.60.255[.]134` — present in the Intel cert cluster
- `38.60.208[.]77` — present in the Intel cert cluster

Three IPs in the same /16 block across two independently-derived pivot chains is a meaningful infrastructure cohesion signal. It is consistent with a single operator managing both sub-clusters from the same VPS provider block, and it upgrades the Intel cert cluster from a tradecraft-similarity inference to a *plausible* infrastructure overlap.

The ShadowPad assessment rests on converging infrastructure signals rather than direct malware telemetry; absent payload recovery, the relationship should be considered provisional.

### Attribution Note

ShadowPad is shared across at minimum APT41, APT27, APT15, Earth Lusca, Tick, Team Tonto, and Webworm. I am not attributing this cluster to any specific group. 

The `moi.gov[.]af` certificate behaviour is consistent with multiple actors with South and Central Asian targeting interests. Attribution would require additional TTP or sample evidence.

---

## Finding 2 — Winnti ELF C2 Infrastructure Extension

### Starting Point

Researcher @TuringAlex[^5] published two SHA256 hashes for confirmed Winnti ELF samples in May 2026:

- `c83e768f3020119dc44392a46f587366c3ef70659592fbafb6cf94f08676bf3b`
- `de155feb28a98a18ae7962ed321c262d80e332b646da6fe8af65d0708167faef`

Both samples use `linux.tklolasi[.]com` as their C2 domain. This extends the Winnti ELF cloud credential harvester cluster documented by Breakglass Intelligence in April 2026[^2], which identified a backdoor targeting AWS, GCP, Azure, and Alibaba Cloud instance metadata endpoints, using SMTP port 25 as a covert C2 channel and Alibaba Cloud typosquat domains for infrastructure camouflage.

The `linux` subdomain prefix is consistent with the ELF targeting profile of this specific tool.

**Confidence: high**

### DNS Pivot

`linux.tklolasi[.]com` resolved to `106.15.148[.]44` for approximately two days. Short resolution windows of this kind are consistent with active operational infrastructure being rotated to avoid blocklisting—not indicative of a parked or sinkholed domain.

**Confidence on IP as operational C2: medium-high**

### Three Alibaba Lookalike Domains

On `106.15.148[.]44`, three additional domains were co-resolving during the same window:

- `ayuncs[.]com` — drops `ali` from `aliyuncs.com`
- `aliyunbs[.]com` — swaps `cs` for `bs`
- `aliyuncs[.]me` — mirrors the CN on a `.me` TLD

All three impersonate Alibaba Cloud's primary object storage domain. This is a direct tradecraft match with the confirmed Breakglass cluster, which uses `ai.aliyuncs[.]help`, `ns1.a1iyun[.]top`, and `ai.qianxing[.]co` — all Alibaba Cloud impersonators. The operational logic is coherent: a cloud credential harvester targeting Alibaba Cloud workloads camouflages its C2 behind Alibaba-lookalike domains.

Co-resolution on a shared IP does not alone confirm same operator. But the specificity of the Alibaba impersonation pattern, in combination with the confirmed sample linkage to the same IP, makes coincidence unlikely.

**Confidence on three lookalike domains: medium**

---

## Cross-Finding Observation

One IP appeared across both investigations: `121.201.109[.]98`. This IP is present in the DragonEgg C2 indicators published by Lookout[^3] in July 2023 and in the LightSpy/DeepData indicators published by Volexity[^4] in November 2024 — both attributed to APT41. It did not directly feature in my pivot chains but its presence in both public clusters and its proximity to infrastructure I identified is worth flagging for other researchers to investigate.

---

## IOC Summary

All IOCs can also be found [here](https://github.com/4rchib4ld/plausible-deniability-iocs/blob/main/chinese_espionage.csv)

**ShadowPad cluster — HTML body hash confirmed (medium-high confidence)**

`cashmicrosoft[.]com`, `googledrivecloud[.]com`, `45.77.176[.]85`, `64.176.65[.]222`, `207.148.97[.]65`, `65.20.76[.]151`, `104.238.148[.]158`, `38.60.250[.]74`, `64.176.50[.]187`, `149.28.128[.]65`, `149.28.145[.]214`, `64.176.229[.]94`, `149.28.159[.]61`, `65.20.97[.]249`, `139.180.211[.]117`, `149.104.104[.]76`, `80.240.16[.]246`, `95.179.254[.]241`, `65.20.75[.]136`

**ShadowPad cluster — Intel cert / pivot chain (medium confidence)**

`195.86.120[.]2`, `38.60.255[.]134`, `38.60.208[.]77`, `65.20.67[.]90`, `139.180.137[.]3`, `103.140.187[.]9`, `103.140.186[.]81`, `193.56.255[.]178`, `rallyracingglobal[.]com`

**Hunting lead — Intel Corporation TLS certificate**

`C=US, ST=CA, L=Santa Clara, O=Intel Corporation, CN=Intel Corporation - Client Components Group` on TCP/443, first observed February 2026, 9 hosts globally.

**Winnti ELF extension**
- Sample linkage: high confidence
- Operational C2 IP: medium-high confidence
- Additional lookalike domains: medium confidence

`c83e768f3020119dc44392a46f587366c3ef70659592fbafb6cf94f08676bf3b`, `de155feb28a98a18ae7962ed321c262d80e332b646da6fe8af65d0708167faef`, `linux.tklolasi[.]com`, `106.15.148[.]44`, `ayuncs[.]com`, `aliyunbs[.]com`, `aliyuncs[.]me`

---

## STIX Bundle

A STIX 2.1 bundle containing all indicators with confidence scores, sourcing, and analytical notes is available on request. Objects authored by Axel / Plausible Deniability are marked TLP:WHITE in the public release. Anchor objects referencing APT41, POISONPLUG.SHADOW, and the Winnti ELF malware family are included for analytical context. Inclusion of these objects should not be interpreted as attribution.

---

## References

[^1]: [Hunt.io — Tracking ShadowPad Infrastructure Via Non-Standard Certificates (February 2024)](https://hunt.io/blog/tracking-shadowpad-infrastructure-via-non-standard-certificates)
[^2]: [Breakglass Intelligence — APT41 Winnti ELF Backdoor: Cloud Credential Harvester with Alibaba Typosquat C2 (April 2026)](https://intel.breakglass.tech/post/apt41-winnti-elf-backdoor-cloud-credential-harvester-alibaba-typosquat)
[^3]: [Lookout — WyrmSpy and DragonEgg Surveillanceware Attributed to APT41 (July 2023)](https://www.lookout.com/threat-intelligence/article/wyrmspy-dragonegg-surveillanceware-apt41)
[^4]: [Volexity — BrazenBamboo Weaponizes FortiClient Vulnerability (November 2024)](https://www.volexity.com/blog/2024/11/15/brazenbamboo-weaponizes-forticlient-vulnerability-to-steal-vpn-credentials-via-deepdata/)
[^5]: [@TuringAlex — Winnti ELF sample hashes (May 2026)](https://x.com/TuringAlex/status/1918667466810798335)