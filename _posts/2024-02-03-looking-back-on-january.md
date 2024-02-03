---
title: Looking back at January
author: michal
date: 2024-02-03 14:18:00 +0100
categories: [Personal]
tags: [recap, tech world, vmware]
render_with_liquid: false
image:
  path: https://michal.grecner.me/hiwaga/vmware_by_broadcom.webp
  alt: VMware by Broadcom
---

The new year came with a bang, and I don't mean the literal one (are fireworks banned yet?). I did something I usually don't: make New Year's resolutions (still 1080p). Most important of them, I set a rule that I never work more hours than I slept. Struggling with it already.

There were some little things in my life regarding technology and business. Still figuring out how to effectively manage projects and get control of my time again. But the most significant news for my career is Broadcom's acquisition of VMware.

VMware products are my bread and butter, and I used to stand firmly for them. Sure, it is an enterprise-grade solution that is absolute overkill for most small businesses. But in larger environments, it never had a real competitor. Microsoft focuses more on Azure than Hyper-V; KVM and Proxmox are great for home labs and small environments, and only a few remember Xen. Nutanix is a new(ish) kid on the block.

And this lack of options became a problem. I would like to explain extensively how VMware will work after acquisition... but I don't know. Barely anyone knows.

We can argue about Broadcom's business decision, if they are good or bad, if they sink VMware or not and only time will tell. But one thing I am sure of. The lack of communication with business partners, and not sharing any information about how the business relationship will work in the (very) near future is a betrayal in the eyes of many. Cost increase? You can manage or talk about. Change in portfolio? Again, manageable. But realizing that Broadcom does not care enough about business, you bring them actually to speak with you about changes; that is something that leaves a sour taste. And I am sure that any manager and business owner worth their salt will remember that in years to come.

To sum up what we know. End-user solutions like Horizon and antivirus Carbon Black are going to be sold. If you are a user, start looking for a new solution. Perpetual licenses are dead; you can only buy VMware as a subscription. They are reasoning with giving you more value but they basically just bundled products. If you use everything in a bundle, you will probably be okay. If not, expect your price to go up. As a result, this will hurt primarily small and medium businesses because large ones already use those products anyway. For me, the most controversial is bundling the vSAN. Many companies still rely on good ol' SAN and have little use for vSAN. I am curious how large companies will handle this. Who knows, maybe it will boost the use of vSAN. From cloud world, Tanzu stays and is a focus. I am not against it. Kubernetes environment is a mess and any newcomer is bound to be confused with options and how they stack together. If you don't trust me, just visit any conference and listen to presentations of DevOps guys. They will probably tell you what stack they are using, and everyone will have it slightly different. Tanzu simplifies adoption, because you get a stack that works together, with support.

The last change I want to talk about is layoffs. It was expected. If a large company buys another large company, there is bound to be some duplicity in roles. But I was surprised to see on LinkedIn that layoffs also affected Ireland's office. Those support engineers were the reason why support worked in the EMEA region. When some names of engineers popped up in the signature to my support ticket, I smiled because I knew it would be a quick and smooth ride to a solution. They were from the Ireland office. Many of them don't work for VMware anymore. They were also the ones who got your ticket if something did not go well and you got to escalate it. What will now happen with support in EMEA? I don't know, but I don't see how it can keep the quality. Sad news for all customers in Europe indeed.

And don't get me started on what mess the disruption of deals with hardware vendors can be.

But as with everything, time will tell. I can't blame anyone looking for an alternative. For some, it will be the final straw to go cloud native. Some will have to adjust their skill sets for Proxmox or maybe Nutanix. Those two sure understood their worth now. Nutanix is bombarded with people wanting to talk to them, and Proxmox started hiring so they can offer enterprise-level support (an excellent move!). Time will indeed tell.

And what is your take on VMware future? What do you look forward to, and what do you fear? Let me know in the comments.