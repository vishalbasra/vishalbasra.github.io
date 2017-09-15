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
