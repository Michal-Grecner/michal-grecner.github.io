---
title: Looking back at Feb, Mar, Apr...
author: michal
date: 2024-05-03 21:29:00 +0100
categories: [Personal]
tags: [recap, tech world, vmware, vulnerabilities, kubernetres, aks, azure]
render_with_liquid: false
---

When I wrote Throwback in January, I wanted it to be a monthly thing. Three months later, I am sure I will have to work on my consistency. I will at least list some news and articles that resonated with me. 

A lot of my work revolves around VMware by Broadcom. Transformation under the Broadcom umbrella is still ongoing. A few things are clearer, and a few things are still unclear, but it is moving forward. 

From the start, we knew that Broadcom wanted to dump EUC or, more precisely, move it elsewhere. They found someone who would like to buy it, and it is the Chinese firm KKR. We will see what it will mean for former VMware customers who rely on their EUC technology. 

<https://www.reuters.com/technology/broadcom-nears-38-bln-sale-remote-access-unit-kkr-2024-02-24/>

The essential news was that Broadcom decided to kill the free ESXi version. For those who never needed it, it was a minimal but fully functional version of the ESXi hypervisor that was provided entirely free. It has some niche uses, and I heard about a few projects that now struggle with transition. Nowadays, you can only try ESXi in the 60-day trial edition, but you have to buy a license afterward. Unfortunately, I also heard about companies relying on the free version of ESXi. I wouldn't want to be an IT administrator in a company that needs to find a solution quickly - either rack up cost significantly or transfer the whole environment to a different technology. Luckily for these admins, Proxmox is lending a helping hand, but we will get to that later. 

Another stir in the aftermath of VMware's transition is the killing of perpetual licenses, the most significant change affecting how companies utilize VMware technology. The obvious goal is to move every single customer to a subscription-based model. It is no surprise that companies struggle under the tight deadline Broadcom has set. The good thing is that Broadcom is aware of the situation, tries to mitigate it, and offers security updates for those who still have a valid perpetual license. 

<https://www.theregister.com/2024/04/16/broadcom_vmware_perpetual_license_support/>

And they even noticed that their customers are not happy how they are handled. Again, there's no surprise there. No one wants to feel insignificant to someone who earns millions from them every year. 

Many of VMware's competitors react to changes in the virtualization business, and as I mentioned before, Proxmox is one of them. They released a new wizard capable of automatically migrating from VMware to Proxmox. 

<https://forum.proxmox.com/threads/new-import-wizard-available-for-migrating-vmware-esxi-based-virtual-machines.144023/>

That is an excellent move from them, and it helps a lot all the small projects and non-profits that need to move fast because they just cannot afford the new VMware price tag. There is also a simple guide available: <https://pve.proxmox.com/wiki/Migrate_to_Proxmox_VE#Automatic_Import_of_Full_VM>

Many of my friends and acquaintances who work with VMware are thinking about and discussing their careers. How does it affect the future of our work, and how can we approach it from a long-term perspective? Many of us (myself included) slept on the next stage of virtualization - containerization. Docker and Kubernetes are here to stay and both developers and ops people widely adopted it. It may be time to extend our view and knowledge this way. I am not one of the evangelists who thinks that everything should run in pods and in public clouds. Far from it, some applications are stateful by design and make more sense in classic VMs, and there is data you want to keep on-premise. I am even bold enough to say that some applications are better off bare-metal (I am looking at you, Oracle database). However, it is time to stop closing eyes before it. That's why I am grateful to my employer for sending me on a hackathon for Azure Kubernetes Service. Well, hackathon... Most of us were complete newbies, so it was more of a practical training. But our lecturers were amazing, and it did the job for me. It raised my interest and gave me some basics to start with. And I even got this fancy virtual badge. Yay!

![Desktop View](https://images.credly.com/images/d6400cf5-68fb-462e-a022-0e22aeed0d50/image.png){: width="300" height="589" }

As I mentioned before, many companies react to changes in the industry that Broadcom started. IBM is no exception, so they intend to buy Hashi Corp, which is known for its IaaC tool, Terraform.

<https://www.theregister.com/2024/04/25/ibm_q1_2024/>

OpenTofu, the open-source fork of Terraform, is on a noble path to maintain the open-source version of Terraform. However, they forked from a version that wasn't open source.

<https://www.infoworld.com/article/3714980/opentofu-may-be-showing-us-the-wrong-way-to-fork.html>

And last bit. I am not fond of re-sharing vulnerabilities on this blog, mainly because hundreds of major vulnerabilities have been found every month since COVID-19. However, in these last few months, some have caught my eye.


<https://thehackernews.com/2024/05/dropbox-discloses-breach-of-digital.html>
<https://thehackernews.com/2024/04/okta-warns-of-unprecedented-surge-in.html>
<https://thehackernews.com/2024/03/new-zenhammer-attack-bypasses-rowhammer.html>
<https://www.bleepingcomputer.com/news/security/new-http-2-dos-attack-can-crash-web-servers-with-a-single-connection/>
<https://arstechnica.com/security/2024/03/backdoor-found-in-widely-used-linux-utility-breaks-encrypted-ssh-connections/>

What an era to be an IT administrator... 

That's it for now, folks, and until next time!