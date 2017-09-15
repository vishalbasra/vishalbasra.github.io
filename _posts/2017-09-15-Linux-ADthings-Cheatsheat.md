---
layout: post
title:  "Linux AD cheatsheet"
categories: samba

categories:
  - Linux
tags:
  - Linux
  - Samba
  - AD
  - Concept
  - Cheat-sheet

excerpt: Cheat-sheat for linux AD stuff - Samba / winbind / etc.
---

**This list is in now way a complete list of all things that can be done with these tools, but I find it useful from time to time**



| Command        | Explanation  |
| ------------- |:-------------:|
| `kinit`      | Asks for the password for the default administrator account and proceeds to get a `Kinit` ticket ; doesn't return a response if no error. |
| `kinit <username>`      | Asks for the password for the username specified and then  proceeds if the user is an administrator to get a `Kinit` ticket ; doesn't return a response if no error. |
| `klist`      | Displays information about the `KerberOS tickets` like where the ticket is from and what's the validation of the ticket. |
| `klist -l`      |  List view of tickets. |
| `klist -d `      | List of KerberOS tickets with `type`.  |
| `klist`      |  List of kerberOS tickets with `encryption`. |
| `net ads leave -U <administrator>`      | Leave the joined Domain |
| `net ads join -S <ad_dc_server> -U <administrator>`      |  The **-S** option is optional and we can join a domain even without specifying the DCT server | DC specified in `/etc/samba/smb.conf` |
| `net ads test join`      | Tests to see if the AD join is sucessful. |
| `net ads dns register -P`      | Register the DNS in-case the DNS registration at the time of domain joining didn't work correctly. |
| `net ads info`      | Information about the domain and domain controller {default LDAP port is **389**} |
| `net ads status`      | Gives extended view of details of the domain, domain controller and membership status |
| `net ads lookup`      | information about the domain-controller - forest , nearest **KDC** - `KerberOS Key Distribution Center`, etc. |
| `getent passwd "<DOMAINNAME>\<username>"`      | Get OS level information about the AD user. Note that the DOMAIN NAMe should be in **upper-case**  |
| `id <user>`      | Query / verify id of a user.  |
| `group <user>`      | Query / verify group of AD user.  |
| ` wbinfo -p`      | Pings winbind to see if winbind is running  |
| `wbinfo -u`      | Pings / lists all the AD users. |
| `wbinfo -g`      | Pings / lists all the AD groups. |
| `wbinfo -i <ad_user>`      | Gets userID , primary group ID , users home directory , loginshell , fullname |
| `wbinfo -a <user>%<passowrd>`      | Will test to see plaintext password authentication and see if the user authentication will work. |
| `wbinfo -t`      | Verifies if linux server account was created when the samba server was added to the windows AD is working ; checks **trust info** for the domain  |
| `wbinfo -r <username>`      | Shows unix group IDs to which the user belongs  |
| `systemctl stop <winbind|smb|nmb>`      | Does what it says it does! |
| `net cache flush`      | Flush winbind's cache |
| `/var/lib/samba/*.tdb`      | Good fedault file's to know |


