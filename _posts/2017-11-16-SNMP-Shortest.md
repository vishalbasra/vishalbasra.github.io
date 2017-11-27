---
layout: post
title:  "SNMP 1/101"
categories: SNMP

categories:
  - SNMP
tags:
  - Concept
  - SNMP
  - Linux

excerpt: World's most shortest SNMP article!
---

## How to install [SNMPWALK](https://linux.die.net/man/1/snmpwalk) ?

- `sudo yum -y install net-snmp net-snmp-utils` 
- A good configuration file to know of `/etc/snmp/snmpd.conf`
- chkconfig snmpd on ( or use systemctl )
- service snmpd restart ( or use systemctl )

## Walk the walk!

`snmpwalk -v2c -c  <community secret> <IP> <OID>` 


The 
- `-c` is the client followed by the client IP 
- `v2c` is teh SNMP version 2


> Note that wehre your're querying from would have to be white-listed at the source