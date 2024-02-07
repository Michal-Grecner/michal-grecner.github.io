---
title: VMware Cloud Director transfer server storage and Hitachi NAS
author: michal
date: 2024-02-07 14:10:00 +0100
categories: [Tech]
tags: [vmware, cloud director, hnas, hitachi, nfs]
render_with_liquid: false
---

Anyone installing VMware Cloud Director must choose their solution for transfer server storage, and many will choose NFS. Many will try to use existing NFS server solution for this. I tried too. However, my existing solution was Hitachi NAS, and since I did not find much about its struggles online, I wrote a short note about my experience.

Transfer server storage is used to share configuration and as a temporary storage for uploads, downloads, catalog items, and such. You can find more about it in the official documentation. It is vital that all cells has access to this storage and can use it without a hiccup.

In theory, the preparation[^walkthrough] of NFS export for Cloud Director should be a quick and easy task. There are a few requirements[^documentation] from the Cloud Director side that should be easy to meet.

Files owned by `root` with permissions set to `750`.

Export with options `rw,sync,no_subtree_check,no_root_squash`.

Now, working in a company with separate teams for technology, this is second-hand information for me, but what I gathered is that HNAS struggles with most of it.

First of all, HNAS is not just simple linux with files on it. They use some kind of virtual volumes and administrators have limited access via console. It is not possible to change ownership of files or manage permission in a way we are used to from bash. Plus, GUI will not allow you to use sync and no_subtree_check.

The issue with ownership of files will break the Cloud Director validator of NFS export. Even when you are trying to add a standby cell, after installation, you will receive an error saying:

> Backend validation of NFS mount failed with: The provided NFS share is owned by an unknown user with UID: xxx, expected root

Those who are more versed in cloud director ways might suggest adding the export via API.

```
{

    "applianceType": "standby",

    "storage": {

    "nfs": "server:/export"

    }

}
```

To bypass the validation and move on with cell configuration. But from my experience, the initialization of the cell will not start either way. 

Can we hack around and force the cell to the cluster? Probably yes. Is it worth the effort and possible issues in the future? Probably no.

The second part of the problem, missing `sync` and `no_subtree_check`, is more a matter of stability and effectivity, but, again, there are more pressing concerns regarding this setup to work with.

You will be better off spinning a simple Linux VM with an NFS server than trying to bend HNAS and Cloud Director to your will against the best practices provided by vendors. Especially when a better solution is quick and easy.

## Sources

[^documentation]: <https://docs.vmware.com/en/VMware-Cloud-Director/10.5/VMware-Cloud-Director-Install-Configure-Upgrade-Guide/GUID-BCC3CFF0-E85A-450C-8A5E-3723DFC1A093.html>
[^walkthrough]: <https://kb.vmware.com/s/article/2086127>