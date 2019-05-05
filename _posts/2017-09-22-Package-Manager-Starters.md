---
layout: post
title:  "Package Managers"
categories: Linux

categories:
  - Linux
tags:
  - Linux
  - Package Manager
  - Cheat-sheet

excerpt: Package Managers for Linux like operating systems
excerpt_separator:  <!--more-->

permalink: /:categories/:title/
---

I'm making this page in the hope that this is will help me with the commonly used ways to interact with `RPM`.
This page is by no means complete and isn't intended to be a tutorial, for a complete turorial and reference, visit the best source for it by typing `man rpm` :)

# RPM

<b>Red Hat Package manager<b>

| Command        | Explanation  |
| ------------- |:-------------:|
| `rpm -qa`      | Prints, rather queries all packages and lists them. |
| `rpm- Uvh package_name`      | Unpacks a package verbosely in human friendlt format, in other words installs the package. |
| `rpm- ev package_name`      | Erases a package verbosely or in other words uninstalls it - This <b>will not<b> remove the files associated with it.  |
| `rpm -q package_name --last`      | Queries the package and lists when was it last updated. |
| `rpm -ql package_name`      | Queries a package and lists all of the files inside the pacakge. |
| `rpm -q --changelog package_name`      | Queries the package and lists the changelog, this is pretty verbose, you might want to pipe a <b>head<b> or <b>tai<b> to it. |
| `rpm -qi package_name`      | This will query the package and list it's information ; kinda similar to `yum info` |

If there's an issue with RPM/YUM as in the Database is corrupt, delete/move the files in `/var/lib/rpm/*__db.*` and rebuild `yum clean all` or `rpm -v --rebuilddb`

# Yum

<b>A nice explanation for yum might go here?<b>
