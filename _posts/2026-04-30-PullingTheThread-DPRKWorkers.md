---
title: "Pulling the Thread: Pivoting on DPRK IT Worker Infrastructure"
toc: true
toc_sticky: true
excerpt: "A simple naming pattern search on DPRK-linked infrastructure — and one domain that kept giving."
show_date: true
categories:
  - Blog
tags:
  - analysis
  - DPRK
---

Team Cymru recently published a [solid analysis of fake IT worker infrastructure](https://www.team-cymru.com/post/dprk-fake-it-worker-cyber-threat-actors-infrastructure), pivoting from `luckyguys[.]site` using X.509 certificates and NetFlow data. If you haven't read it, start there.

One question came to mind after reading it: are there other domains following the same naming pattern, registered around the same time?

## The search

I searched for domains following a luckyguys naming convention, combined with similar registration timing and exposed services. One result stood out: **`luckyguys[.]cloud`**. The domain was registered January 6, 2026, one month after `luckyguys[.]site` (December 2, 2025) with the same registrar (Hostinger). 

| ![]({{site.baseurl}}/assets/images/pivoting_dprk/luckyguys_site_WHOIS.png) | ![]({{site.baseurl}}/assets/images/pivoting_dprk/luckyguys_cloud_WHOIS.png) |
|:--:|:--:|
| *`luckyguys[.]site` WHOIS record* | *`luckyguys[.]cloud` WHOIS record* |

It also hosts a Gitea instance. These characteristics are consistent with those observed on `luckyguys[.]site`. 

| ![]({{site.baseurl}}/assets/images/pivoting_dprk/luckyguys_site_git.png) | ![]({{site.baseurl}}/assets/images/pivoting_dprk/luckyguys_cloud_git.png) |
|:--:|:--:|
| *`git.luckyguys[.]site` hosting a Gitea instance (source: Validin)* | *`luckyguys[.]cloud` displaying a Gitea Welcome Page (via [urlscan.io](https://urlscan.io/result/019cbe1a-44a5-739b-9a56-228b7806b480/))* |

## What made it interesting
IP `45.15.167[.]146` hosts all `luckyguys[.]cloud` subdomains. Its PTR record resolves to `rbluckyguys[.]com`. And the exposed login panel references "RB Luckyguys Management."

| ![]({{site.baseurl}}/assets/images/pivoting_dprk/rb_luckyguys_panel.png) |
|:--:| 
| *Panel available on luckyguys[.]cloud/login ([via urlscan.io on January 13, 2026](https://urlscan.io/result/019bb4ad-bec5-7619-9822-0731fbedd763/))* |

The naming linkage extends beyond the primary domain, appearing in PTR records and application artifacts. It suggests a consistent ‘Luckyguys’ naming reuse across this infrastructure.

The subdomains also hint at an instant messaging interface (message.luckyguys[.]cloud and msg.luckyguys[.]cloud). This is consistent with the interface observed on `luckyguys[.]site` on April 8, 2026 (via [urlscan.io](https://urlscan.io/result/019d6c23-ddea-751d-a0f5-89426437ef69/)).

| ![]({{site.baseurl}}/assets/images/pivoting_dprk/luckyguys_site_msg.png) |
|:--:| 
| *Login panel found on `luckyguys[.]site/login`* |

## The abandonment pattern
The domain resolves to significantly more subdomains than `luckyguys[.]site` (18 vs 5). Per urlscan.io snapshots, the apex domain was reachable in January and March 2026. None of the endpoints respond at the time of writing. It is consistent with infrastructure torn down following public disclosure, as documented in the original Team Cymru post.

| ![]({{site.baseurl}}/assets/images/pivoting_dprk/luckyguys_site_sub.png) | ![]({{site.baseurl}}/assets/images/pivoting_dprk/luckyguys_cloud_sub.png) |
|:--:|:--:|
| *`luckyguys[.]site` subdomains* | *`luckyguys[.]cloud` subdomains* |

## Attribution
Moderate confidence. Overlap in naming, infrastructure, and artifacts is suggestive, not conclusive. No direct IP overlap with the infrastructure documented by Team Cymru was identified, suggesting this may represent a separate but potentially related infrastructure segment.

**IOCs (observed subdomains and related infrastructure)**
```
luckyguys[.]cloud
rbluckyguys[.]com
admin.luckyguys[.]cloud
api.luckyguys[.]cloud
cdn.luckyguys[.]cloud
chat.luckyguys[.]cloud
check.luckyguys[.]cloud
clients.socket.luckyguys[.]cloud
ext.luckyguys[.]cloud
file.luckyguys[.]cloud
git.luckyguys[.]cloud
main.socket.luckyguys[.]cloud
manage.luckyguys[.]cloud
message.luckyguys[.]cloud
msg.luckyguys[.]cloud
rdweb.luckyguys[.]cloud
rustdesk.luckyguys[.]cloud
socket.luckyguys[.]cloud
```