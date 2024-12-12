---
title: Disable notification about SAML certificate expiration in Cloud Director
author: michal
date: 2024-12-12 20:51:00 +0100
categories: [Tech]
tags: [vmware, cloud director, saml, certificate, notification]
render_with_liquid: false
---

VMware Cloud Director has a feature where your tenants can use their Identity Providers to log into your portal. SAML certificates are created with the Virtual Data Center. They expire after a year, and you, as a service provider and tenant admins, will get an email about it. But if you have a lot of tenants who do not use it, that is a lot of spam. And naturally, you'll start to wonder if you can get rid of it.

I was wondering that, too. Many tenants are confused and a little bit scared about notifications that warn them about losing some functionality, even if they don't use it. So they ask their cloud support about it, and you get even more spam. This time, you need to answer in a way that reassures the client they have nothing to worry about.

Now, if you came here hoping I would give you some workaround to disable these notifications, I have bad news for you. I will not (well, kinda), but I will show you why it is not possible at the moment so you don't have to waste your time figuring it out yourself. Let's get to it.

### The UI

The first thing I was looking for was an option to **disable SAML** entirely. However, certificates are generated automatically upon the creation of a VDC. There is no option to disable it. The second thing I was looking for was to **disable notifications** about it. Again, no luck. Not on a global or organizational level. There is no option to change it in the UI. The last thing I was thinking about was to just **remove certificates** so there is nothing to notify, right? Again, not possible. The option to delete the certificate is not present in the UI for new or expired certificates. But we are not giving up just yet. 

### The API

This part will be short since I have a bit of a hate relationship with Cloud Director API. There is a certificate management function, but I was not able to remove it with it. I gave up pretty soon, but after discussing it with Broadcom support, I am pretty sure it would not work either way.

### The Database

When UI and API fails us, there is still a direct access to database. You know, the one you should not use on production systems without support engineer. Just a fair warning and reminder to do backup before anything adventurous. 

Now, when you poke around the DB for a bit, you will find some interesting things. 

There is a table called certificate_library_item and when you search it with name of the SAML certificate from the UI, you can find it here:

````console
vcloud=# select * from certificate_library_item where alias='SAML Encryption-2023-01-25-14-11-27';
-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
id                     | 32208a14-2cd9-4432-b974-fbebe7446e6c
alias                  | SAML Encryption-2023-01-25-14-11-27
certificate            | -----BEGIN CERTIFICATE-----
                        (REDACTED)
                       | -----END CERTIFICATE-----
private_key            | -----BEGIN ENCRYPTED PRIVATE KEY-----
                        (REDACTED)
                       | -----END ENCRYPTED PRIVATE KEY-----
                       |
private_key_passphrase | (REDACTED)
description            | SAML encryption certificate and key
org_id                 | 59614eb0-d178-4876-aed2-be960e4620dc
````

A-HA! You got it! Just delete this entry and it's gone! Right?

Well, no.

````console
ERROR:  update or delete on table "certificate_library_item_consumer" violates foreign key constraint "fk_fed_enc2cert_lib" on table "federation_settings"
DETAIL:  Key (id)=(f5c98f33-cdd5-41ad-8b6b-ad95e038d7dc) is still referenced from table "federation_settings".
````

All right, we can deal with this.

````console
vcloud=# select id,alias,org_id,certificate from certificate_library_item where alias like '%SAML%' and org_id ='59614eb0-d178-4876-aed2-be960e4620dc';
-[ RECORD 1 ]-----------------------------------------------------------------
id          | d5bc8888-379a-453e-b4fb-78c7a980323f
alias       | SAML Signing-2023-01-25-14-11-27
org_id      | 59614eb0-d178-4876-aed2-be960e4620dc
certificate | -----BEGIN CERTIFICATE-----                                     +
			(REDACTED)
            | -----END CERTIFICATE-----
-[ RECORD 2 ]-----------------------------------------------------------------
id          | 32208a14-2cd9-4432-b974-fbebe7446e6c
alias       | SAML Encryption-2023-01-25-14-11-27
org_id      | 59614eb0-d178-4876-aed2-be960e4620dc
certificate | -----BEGIN CERTIFICATE-----                                     +
			(REDACTED)
            | -----END CERTIFICATE-----
````

Here we go!

````console
vcloud=# delete from certificate_library_item where id='d5bc8888-379a-453e-b4fb-78c7a980323f' and org_id ='59614eb0-d178-4876-aed2-be960e4620dc';
ERROR:  update or delete on table "certificate_library_item" violates foreign key constraint "fk_enti_cert_use2server_certif" on table "certificate_library_item_consumer"
DETAIL:  Key (id)=(d5bc8888-379a-453e-b4fb-78c7a980323f) is still referenced from table "certificate_library_item_consumer".
````

You've got to be kidding me.

````console
vcloud=# select * from certificate_library_item_consumer where cert_library_item_id ='d5bc8888-379a-453e-b4fb-78c7a980323f';
-[ RECORD 1 ]--------+----------------------------------------------------
id                   | 4b02b7a1-dbf0-4c54-ba56-9dde70e3f956
entity_urn           | urn:vcloud:org:59614eb0-d178-4876-aed2-be960e4620dc
entity_name          | MyOrg
cert_library_item_id | d5bc8888-379a-453e-b4fb-78c7a980323f

vcloud=# delete from certificate_library_item_consumer where cert_library_item_id ='d5bc8888-379a-453e-b4fb-78c7a980323f';
ERROR:  update or delete on table "certificate_library_item_consumer" violates foreign key constraint "fk_federa_settin2cer_lib_it_co" on table "federation_settings"
DETAIL:  Key (id)=(4b02b7a1-dbf0-4c54-ba56-9dde70e3f956) is still referenced from table "federation_settings".
````

Of course.

````console
vcloud=# select * from federation_settings where signing_cert_consumer_id='4b02b7a1-dbf0-4c54-ba56-9dde70e3f956';
-[ RECORD 1 ]----------------+-------------------------------------
org_id                       | 59614eb0-d178-4876-aed2-be960e4620dc
key_store                    |
signing_expiration_date      | 2024-01-25 14:11:27
is_sign_cert_exp_notified    | t
entity_id                    |
role_attribute               |
email_attribute_name         |
user_name_attribute_name     |
first_name_attribute_name    |
surname_attribute_name       |
full_name_attribute_name     |
group_attribute_name         |
signing_cert_consumer_id     | 4b02b7a1-dbf0-4c54-ba56-9dde70e3f956
encryption_cert_consumer_id  | f5c98f33-cdd5-41ad-8b6b-ad95e038d7dc
encryption_expiration_date   | 2024-01-25 14:11:27
is_encrypt_cert_exp_notified | t
````

Hold on right there. 

````console
is_encrypt_cert_exp_notified
````

That sounds like it! Again, we got excited too soon. I discussed this with Broadcom Support and engineering is adamant that you should not change this to false under any circumstances. And I decided to believe them. So, back to deleting, which is safer option. I guess.

````console
vcloud=# delete from federation_settings where signing_cert_consumer_id='4b02b7a1-dbf0-4c54-ba56-9dde70e3f956';
DELETE 1
vcloud=#  delete from certificate_library_item_consumer where cert_library_item_id ='d5bc8888-379a-453e-b4fb-78c7a980323f';
DELETE 1
vcloud=#  delete from certificate_library_item where id='d5bc8888-379a-453e-b4fb-78c7a980323f' and org_id ='59614eb0-d178-4876-aed2-be960e4620dc';
DELETE 1
vcloud=# delete from certificate_library_item_consumer where cert_library_item_id ='32208a14-2cd9-4432-b974-fbebe7446e6c';
DELETE 1
vcloud=# delete from certificate_library_item where id='32208a14-2cd9-4432-b974-fbebe7446e6c' and org_id ='59614eb0-d178-4876-aed2-be960e4620dc';
DELETE 1
````

Victory! Or is it? 

### The Aftermath

I should have taken screenshots of this. This is on me, really. But you will have to trust me on this. Removing all certificates from the VDC basically disables SAML for it. However, with no certificates gone, it also removes the SAML option from the menu in the UI.

And the menu is not built for that. CSS broke slightly. More importantly, nothing in the Organization Administration panel works. You cannot edit users. You cannot edit groups. Nothing.

So this is the point where you admit your defeat and restore from backup. Spam is bad, but a broken UI is even worse.

### There has to be something...

You can't disable it from the Cloud Director. All you can do is post a feature request (as I did). But with the uncertainty of Cloud Director as a product after version 10.7, I don't know if it's really a way.

However, if you have control over your SMTP relay, you can at least filter these messages before they are sent out to your tenants. It is a wonky solution, but better than none.

So here we are. You read about this journey, you know why, and we can be grumpy about it together. I would like to thank Broadcom support for having patience with me when I tried really hard to find some solution for this on the application side. But hey, at least we know and can manage. I hope this saves you some time!