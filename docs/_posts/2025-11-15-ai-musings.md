---
layout: post
title:  AI Musings - How I used AI to quickly manage my DNS migrations
date:   2025-11-15 11:20:33
categories: ai-musings
post_id: 14
---

A lot has happened since I last wrote anything here. I have been meaning to, but life keeps getting in the way. I have started a new adventure, [Deterrence Technologies](https://deterrence.ng), got a few contracts, and battled with all the formalities and challenges of running a startup in Nigeria. Overall, Alhamdulillah, it's been awesome (if you enjoy pain).

The best time to write about something is while it's still fresh in your mind. Since the AI race started, I haven't written anything down about my experiences playing with AI tools. Moving forward, I intend to change that, so here goes.

## DNS Migration

When I decided to register a domain for Deterrence, I chose a `.ng` domain since `deterrence.com` was taken. I registered the domain with **Go54** formerly, [Whogohost](https://whogohost.com), probably the most popular Nigerian domain registrar, I do not recommend them, their experience is not so great, but I think the **Go54** acquisition is changing the tide. A few weeks down the line, I've added a couple of DNS records, when I suddenly realized they had a limit on the number of records I could add, an annoyingly low limit of 10 records per record type, essentially meaning I could only add 10 `A` records, 10 `CNAME` records, etc. This was an unacceptable limitation, since our infrastructure was growing rapidly and we needed to manage more records to ensure our services were effective. So I needed to migrate my DNS records management to a more flexible provider like **GCP Cloud DNS** since we were already using GCP for most of our infrastructure. DNS isn't something anyone likes to mess with once in production, so I found a quick hack and kept procrastinating for a couple of weeks until I finally decided to bite the bullet and get it done last night.

The solution is to switch the domain's nameservers to point to the new DNS provider, in this case, GCP Cloud DNS. The challenge is migrating all the existing records from **Go54** to **GCP Cloud DNS** without manually copying each record, which is tedious and error-prone. I was together with [@CyberGuru](https://x.com/cyyberguru) and he suggested we write a script a script to automate the process, but I was feeling lazy and usually avoid 1-off automations, so I thought, let's see what AI will do (spoiler alert: it solved it in one prompt). We chose to use **Gemini Pro** on the assumption that it should have more knowledge about GCP services since it's a Google product. I manually created a DNS zone in GCP Cloud DNS, added the NS records provided by GCP to **Go54** to point the domain to GCP Cloud DNS and then switched the nameservers for the domain to GCP's nameservers. The last step was to add all the existing records from **Go54** to GCP Cloud DNS.

### A 2-minute fix with Gemini Pro

![Go54 DNS Records](https://ethic41.github.io/assets/images/posts/ai-musings-dns/dns-records-1.png)

I simply took a screenshot of the DNS records page on **Go54** as above and prompted Gemini Pro with the following:

> "I have the following records on a DNS provider, I am changing my name servers to point to GCP cloud DNS, generate a gcloud command for me to add these records to my DNS zone name `a-deterrence-zone` and domain name `deterrence.ng`":

And within seconds, it generated the following command:

![Gemini Pro Generated Command](https://ethic41.github.io/assets/images/posts/ai-musings-dns/gemini-response-1.png)

Gemini Pro was smart enough to batch the tasks into a GCP transaction and execute all at once. I tested for a single record first, and it worked perfectly. I then inspected the code to be sure there were no surprises and then I ran the full command, and within a few seconds, all my records were added to GCP Cloud DNS. Within a few hours, the DNS propagation was complete, and everything was working perfectly. Previously, this would have been an error-prone task that would have taken hours to complete, but with the help of AI, it was done in minutes with minimal effort. I've redacted the actual command since it contains actual information about my DNS records (I know it's easy to find...but at least you have to do some work, xD). Also, the records and commands are much longer than shown in the screenshots; I just cropped them for brevity.

PS: Part of the reason I haven't been writing is that I want to move most of the writings here to [Deterrence Technologies Blog](https://deterrence.ng/blog), since it's where I spend most of my time nowadays. So I will be cross-posting most of my future writings there, and keep this blog for more personal musings and notes (I have a lot of interesting tales to share on my previous adventures).

ما شاء الله كان و ما لم يشاء لم يكن
