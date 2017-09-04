---
layout: post
title:  "Samba as DC"

categories: 
  - Linux
tags:
  - Linux
  - Samba
  - AD
  - Concept

excerpt: How to make a linux machine a Domain Controller!
---
# Samba as AD domain controller 

Quick AD recap 

- `LDAP` - users ; groups ; servers/computers ; services ; application-data 
- Domain is `a unit of administration` , having atleast one domain controller , most likely two(replicated) and might also have other sub domains which form trees and forests
- Samba 4 has been in development for 10 years and was released in 2012 
- The old daemons `smb` , `nmb` and `winbind` still work and support the _domain member_ role just as same as Samba 3
- There is a new daemon simply called `samba` which supports the _Active Directory Domain Controller_ role 
- New command line administration tool called `sabma-tool` and `swat` is no longer supported 

- Samba 4 as a Domain Controller needs to provide three services 
  - `LDAP` service ( to store and access info )  
  - `DNS` services ( to locate servers ) 
  - `Kerberos` ( authentication ) 


Samba 4 commonly used ports 

**Traditional file server role**

- 135	TCP	Service location service for `RPC` or Remote Procedure Call
- 137	TCP	`NetBIOS name resolution` ,NetLogon
- 138 	UDP	`NetBIOS Datagram service` 

**LDAP**
- 389	TCP	`LDAP`
- 686	TCP	LDAP over SSL ( this is called `LDAPS` somethimes ) 
- 3268	TCP	LDAP global catalog searches 
- 3268	TCP	LDAP global catalog searches over

**Kerberos**
- 88	TCP	kerberos version 5
- 88	UDP	kerberos version 5
- 464	TCP	kerberos set/change password
- 464	UDP	kerberos set/change password

**DNS**
- 53	TCP	DNS
- 53 	UDP 	DNS


## Building Samba 4 from scratch 

- Have the nameserver pointed to the server's IP
- Then a bunch of packages have to be installed 
- Choose kerberos realm, servername ( this server after adding it into it's own hosts file ) , administrative server (same machine) 
- git clone the samba code 
- There's a `configure` script and this script will make a `makefile` for us to build samba! 
- The source code is not specific to `linux` or anything else!
- `./configure --help`
- notable options like `with winbind` , `with ads` , `--prefix` option defines where to install samba to like `/usr/bin/samba` etc. 
- example run `./configure --enable-selftest` ; it'll run a lot of tests for system configuration! 
- this will then generate a `makefile` and to use that make file to install the program, simply type in `make` once that's done, do `make install`
- You might want to add the samba programs to your search path like this ` PATH = $PATH:/usr/local/samba/sbin:/usr/local/samba/bin` in a startup script like bashrc / profile etc. 
- This will `NOT` and I repeat *NOT* be hooked into init

## Provisioning a Domain!

- We're going to use `samba-tool` which is a great complex tool and it's help is here at `samba-tool --help`
- Then if you want to see more detailed information about a particular section / command like say `user` for user management, do `samba-tool user --help`
- Similarly keep going down like this `samba-tool user add --help` to show more detailed information about a particular option 
- To simply make a domain `samba-tool domain provision`  then give 
  - REALM
  - Domain NAme ( this is the `netBIOS` domain name, not the DNS name ) 
  - Server role would be `dc` for a domain controller 
  - DNS server ( can be bind yay! ) , even Samba has an internal DNS server  
  - DNs forwarder IP ( if needed ) or is usually the same server itself 
  - Set an `Administrator` password , this is *important*
  - Ensure the current date and time via NTP 
  - ( side note `samba -v` prints the samba version ) 
- To run it, simply `samba` and again, no message is a good message.
- To see where we're listening on from samba `lsof -i | grep '^samba.*IPv4'`
- To verify, one can 
  - Verify smb shares 
  - Verigy kerberos and see if you can get the `TGT` 
- Then create a samba user like this `samba-tool user add Vishal 'password-forVishal'`
- You can list the users like `samba-tool user list`

## Join a windows machine and administer the domain 

- Point the machine to the correct DNS server ( which is the domain controller itself ) 
- My Computer > Right Click > Advanced system Settings > Computer Name Tab > Change > Choose computer name > Choose Domain and enter the domain name 
- then supply the account credentials for an account which has the privilages to join things to a domain! 
- Restart the computer 
- To administer tool, we can use `samba-tool` or `Remote Server Admiinistration Tool` which is a free tool from MS
- Then Control Panel > Programs and Features > Turn Windows features on or off > enable Remote Server Administration Tools 
- To test, add a user from the Remote Server Administration Tool and list the users from samba-tool 


### Now the domain is there and a windows machine is connected to it and also the domain can be managed using the familiar AD management tools!
