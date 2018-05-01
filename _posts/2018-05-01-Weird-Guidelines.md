---
layout: post
title:  "Weird guidelines"
categories: Linux

categories:
  - Everything

tags:
  - Everything


excerpt: Some weird things I've seen or heard
---

**Database Reads**
> Committed Read : Data can only be read when a transaction is complete. i.e Data cannot be read in the middle

> Uncommitted Read: Data can be read even in the middle of a transaction i.e even when it's not saved. Also called "Dirty Read"  

__SQL Server - default is COMMITTED READ__
__PGSQL does not support UNCOMMITTED READ__

Bottom line? : Unless an application requires a COMMITTED read (payment processing, auth, e-mail) , if it uses UNCOMMITTED reads, that's better otherwise it may end up impacting DB performance and lead to blocking.


---
