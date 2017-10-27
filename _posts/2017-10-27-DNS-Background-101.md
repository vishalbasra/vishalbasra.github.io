---
layout: post
title:  "DNS Backgroung and 101"
categories: DNS

categories:
  - DNS
tags:
  - DNS
  - Bind
  - History
  - Concept 
  - general

excerpt: A breif DNS history and how it works
---

Everyone by this point of time knows and understands the Internet's history. You've read all about ARPANet and how the Internet came into being so I won't be repeating that. But here are a few key things that happened 

- `DARPA` funded `ARPANET` in 1960's
- `TCP/IP` developed in 1980's and adopted by University of California's `BSD Unix` ; `ARPANET` is thus the backbone of the Internet
- `DARPA` kills `ARPANET` and replaces it with `NSFNET` which was funded by `National Science Foundation` of the United States in 1988. This means that **the backbone of the Internet now shifted from ARPANET to NSFNET**
- In spring of 1955 the Internet's backbone shifts from `NSFNET` (which was publically funded) to many commercial backbone's run by telecomm / network companies 

> There's a difference between the "Internet" and the "internet". The "Internet" is the network that we all use and generally identify as the "net". An "internet" is any network that's interconected. It doesn't even necessarily have to use the TCP/IP stack.

> The "intranet" is a TCP/IP based "internet" and the intra is used to distinguish an :internet" which specifically uses TCP/IP as the networking stack.


A History of DNS
---------------- 

- In 1970's  ARPAnet was a community of a few hundred hosts and had a single file called **HOSTS.TXT** was maintained which contained name-to-address mapping for each host connected to the ARPAnet.
- The unix `/etc/hosts` file was compiled from this **HOSTS.TXT** file ( by removing feilds that unix didn't need
- Now the **HOSTS.TXT** file was maintained by <a href="https://www.sri.com/">SRI</a>-NIC ( you may also want to see <a href="https://www.sri.com/work/timeline-innovation/timeline.php?timeline=computing-digital#!&innovation=domain-names-network-information-center">Timeline on SRI</a> and <a href="https://www.sri.com/about/timeline/tld-nic">SRI's Role in managing NIC</a>
- `ARPANET` administrators would email changes to the NIC(the SRI-NIC is also referred to just NIC at this time) with changes in the **HOSTS.TXT** 
- Or they would `FTP` into the SRI-NIC, grab the current **HOSTS.TXT** file and make changes.
- A new **HOSTS.TXT** file would then be compiled once or twice a week or at an even slower pace.


Now obviously there are apparent problems to this today that we can comprehend but at that point of time, the scale of rise of the Internet (ARPAnet at the time) was completely un-precendeted.

As the number of hosts in ARPAnet grew, changes to the **HOSTS.TXT** file also grew propotionately. This not just meant frequent updates in a file which was un-managable, but also `traffic` to the NIC increased dramatically.


This problem kind of escalated into a whole dimensiton when ARPAnet moved to `TCP/IP` thus the population of the network exploded 
- Traffic to `NIC` became a lot
- Load in distrubiting a file on a big and ever-growing network was a lot
- `NIC` had no authority over hostanmes or wasn't assigining hostnames ( as DNS wasn't made yet!) so different hosts had the same hostname, which was problematic
- Consistency was a **big** problem. By the time a **HOSTS.TXT** file reached a far-flung shore of an ever-growing ARPANET, the file could have already changed.

> Long story short, the HOSTS.TXT solution didn't scale with a big and growing network.

So ARPAnet decided to launch an investigation to find a sucessor to the `HOSTS.TXT` and come up with a better system that 
1. Allows local administration of data
2. But also make data available globally 
3. Would have a system for uniqueness of the names

`Paul Mockapetris` who at the time was in **USC's Information Sciences Institue** was responsible for designing the architecture of this system and in `1984` released `RFC 882` and `RFC 883` which described something called the **DOMAIN NAME SYSTEM**

> These RFCs were later superseded by RFC1034 and RFC1035 which have also been enhanced by further RFCs for security and imlementation etc. but the core idea remains the same.



DNS 101
---------

The system is like a distributed Database. Control of the  tables in this database is given to processes locally, yet the database with each table is available locally and is distrubited in the clinet-server architecture. Of-course it employs a lot of schemes to achive this, replication, caching etc.

But I'm trying not to cover what most DNS 101 articles would cover, how TLD's are organized etc. , I'm trying to cover how the system was designed and why so. 

Consider something that is familiar, the **unix system** , how is it designed? Also compare it with how the **Domain Name System** is designed. 

| Unix File System        | Domain Name System  |
| ------------- |:-------------:|
| The name! (System) `/`     | The name! (System) |
| root is at `/`     | root is at `.` |
| has `directories` and `sub-directories`     | has `domains` and `sub-domains` |
| sub-directories like "etc" or "users" or "var"     | sub-domains like "com" or "org" or "net" etc. |
| a useful path be like - say `/var/lib/rpm/` what this means is "Root,var,lib,rpm" so when someone says `cd /var/lib/rpm` semantically that translates to `go to root, then go to var, then go to lib, then go to rpm` but you already know that     | a useful path would be say `awesome.somesite.org` and we know that this means that `awesome` is a sub-domain of `somesite` which is a subdomain in `org` which is a sub-domaion(though it is a TLD) in the `root or .` domain |
| Each `Directory` or `sub-directory` has a unique name inside it's relevant structure(locally if you will)     | Each `Domain` or `sub-domain` has a unique name inside it's relevant structure(locally if you will)   |
| Identifies directories by the path from root(absolute path)    | Identifies domains by the path from root(domain name) |
| sub-directories distinguished in the absolute path by root (`/`)     | sub-domains distinguished in the absolute path by root (`.`)  |
| we can mount another file-system say mount `/coffee-is-awesome` from another host, then that host has the authority of all the `sub-directories` within the `/coffee-is-awesome` which is delegated to it     | each `Domain` can be broken down into `Sub-Domains` and thei authority / delegation can be further given ahead eg. a provider say manages the `.org` domain, but it gives the authority for the subdomain with the `.org` domain , `white-turtle` to me. |
| root is at `/`     | root is at `.` |



> The difference is that the structure of writing names in the `Unix Filesystem` and the `Domain Name System` is different, i.e inverted, imagine having to do `.org.somesite.awesome` that could work too, but it kinda seems un-natural (perhaps because I'm used to it for DNS now?)  The idea in it's essence remains the same though.


> The domain name system is designed just like the unix file-system, only that how it writes the path is inverted.
