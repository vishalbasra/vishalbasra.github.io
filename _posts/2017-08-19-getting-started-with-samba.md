---
layout: post
title:  "Samba 101"
date:   2017-08-19 08:32:48 +0000
categories: samba
author: Vishal Basra
---
---
# Getting Started with Samaba! 
---


<br />I'm writing this post with the idea of how I first learned about samba, and I intend to write this post as a reference for me or for someone to read it and understand the key concepts.
This is not going to be a details Step by Step Technical walkthrough, rather a post that'll work to understand the key concepts.

But before we can delve into Samba and how it works, it's important to understand 

## The most shortest history of Windows Networking wrt this post
<p>1. In 1985,</p> Microsoft didn't support TCP, so they made SMB or Server Message Block work over something called the `NetBIOS` protocol. <br />
   The NetBIOS protocol was MS's solution at the time to do a couple of things ( more in detail later )

It allowed 

- file sharing withing workgroups 
- printer sharing within work groups 
- Ran over NetBIOS and NBF - ( `NetBIOS Frames` )
- identified machines by a NetBIOS name - Yes, MS did not use the now standard `DNS names`
- machines had no underlying numeric address ( so no need for name resolution )

The NetBIOS protocol was not routable and was intended for a bunch of machines on the same network.  
Soon NetBIOS was encapsulated over TCP ( MS learned it) 

<p>2. In 1987,</p> Microsoft learned to encapsulate NetBIOS traffic over TCP and called it  NBT - NetBIOS over TCP </p>

- Name resolution was done like (NetBIOS name -> IP address ) and this was handled by 
   - Lookups were handled in a `lmhosts` file , this is analagous to the `/etc/hosts` file 
   - Central name service called WINS 
   - Broadcasting 

- Machines could register their NetBIOS name and IP address to WINS


<p>3. In 1996,</p> SB continued to evolve and in 1996, it was rebranded as `CIFS` or `Common Internet File System`

- It ran directly over TCP / IP 
- NetBIOS was not used anymore 
- The old NetBIOS way of name resolution was changed to give way to DNS name resolution 

<p>4. In 1993,</p> Windows NT introduced a concept of `domain` 

- It's a set of resources protected via a single network wide login, it's intended to be used as a SSO for a single network 
- There's a Primary DC which holds authorative account information 
- There's also a Backup DC which holds copies and is in use to ensure availibility  

<p>5. In 2000,</p> MS replaced the NT style domains with `Active Directory Domains` 

- These were similar in spirit with the NT domain, but certain things were different 

- These replaced the PDC and BDC roles with multi-master replication 
- Used technologies like :- ( These combine to make Active Directory ) 
  - LDAP as the directory service 
  - KerberOS for authentication 
  - DNS for name resolution 

<p>6. Further versions of windows servers,</p> the `Domain controller` role was named to `Active Directory Domain Services` via the `Active Dirctory Domain Controller` role
in 2003, 2008, 2012 and so on. 

This is one of the roles - `Active Directory Domain Controller` that Samba is able to assume. 

## Samba Mamba!

1. Samba was named after SMB and was created by an Australian programmer called Andrew Tridgell  with Jeremy Allison, based on the reverse engineering of the SMB protocol using a packet sniffer. 
  - Development started in 1992, and it's now a project with ~ 2 dozen active contributers at [Samba](https://www.samba.org)
  - GNU General Public License
  - no Client Access Licences ( CALs ) 

2. Samba Roles 

- Standalone roles 
  Samba does not rely on any other machine to store account info or the authentication and can do
  - File Server 
  - Print Server 
  - WINS ( Almost absolute ) 

- For windows domains, samba can do
  - Domain Member Server 
  - windows NT PDC ( historical interest,is otherwise obsolete )
  - Active Directory Domain Services ( happened in *samba 4* ) - this is the current main DC role that MS calls 


## The daemons of the Samba Mamba

- `NMBD`
  Provides NetBIOS Name Resolution ( WINS ) and network browsing services 

- `SMBD` 
  The main SAMBA hot-shot daemon provides 
    - file sharing 
    - printer sharing 
    - authentication services using the SMB protocol 

- `winbindd` 
  This is not a proper service on the windows side, but it's a service on the linux side that provides a sort of bridge from Linux to Active Directory 

- `Samba`
  This is a new daemon(not at the point of writing this post but relatively), simply called 'Samba' in Samba 4 that allows samba to assume KerberOS roles and act as an Active Directory Domain Services DC 


## Samba command line tools 101

- `smbclient`
Connect to a file share, list contents, this is very much like a command line FTP client 

- `smbpasswd` 
This manages samba's local store of user accounts and passwords, so this is very important for stand-alone servers.  

- `testparm` 
Checks the syntax of the configuraton file smb.conf

- `wbinfo` 
This utility is used to query the winbindd daemon. This is a diagnostic tool. 

- `smbmount` 
A 'client' side program that mounts an SMB file system onto a mount point in the linux FileSystem. useful for when Linux is acting as a client, to mount the shared drive. 

- `smbstatus` 
A program that reports the current network connections to the shares on a Samba server 

- `net` 
A tool for administration of remote SMB/CIFS servers intended to resemble windows "NET" applications 



## A harmless file to know about

`/etc/smb.conf`

- This file is read by the smaba daemons and some comand line tools and is the _MAIN_ configuration file for Samba

- It's written using the old Windows '.ini' format 

- This is the configuration syntax type ; -> ;  Sections contain name = value 

- There's a web based tool called SWAT ; Samba Web Administration Tool ( no longer suported for Samba4 )


## Installing Samba examples sample

Some distrubitions contain client side samba packages like winbind, samba common , samba client etc. 

To see `rpm -qa | grep samba`

 Server components have to be installed 

`yum -y instlal samba samba-docs`

^ This might not install Samba 4 but Samba 3 depending on the target system

- The Samba-doc packages has pretty nice PDF documents! 
  look at those using `rpm -ql samba-doc | less` this will show you the contents of the package, including the PDF files


## Creating and viewind a Samba file share! (finally...)

- in `/etc/samba` , the file `smb.conf` contains a lot of comments which can be useful to understand stuff!

- The `[global]` section of the file contains things that'll affect the server as a whole!

Let's set it like this 

```
[global]
  netbios name = MAMBA-SERVER 
  security = user # this means that authentication will happen based on credentials that are locally stored on the server itself. 
  name resolve order = wins bcast # this might not be absoulte necessary, but the course aythor deemed it to be so. 
  # if this is given like this, then we have explicitly removed DNS


[docs] # This is the name of the share 
  comment = documentation # maybe this is called coment 
  path = /usr/share/doc
  read only = yes 
  # or it can be called writable = no with the same effect 
```

> The traditional NFS UNIX file sharing is different, in NFS the name which the clients see is exactly the path in the server but in Samba we can, like the share name is 'docs' but the path is different.


- To test the syntax use testparam and simply run simply run 
`testparam` and it'll read `/etc/samba/smb.conf` 

- `testparam` in it's output might add a new line like this in the global section 
   `idmap config * : backend = tdb` this has got to do with how the user names and passwords will be saved on the server 

- `service smb start` 

- `service nmb start` 


- `lsof -i | grep '[sn]mbd'` to see that the services are listening on TCP and UDP ports!

- if the firewall is enabled, disable it. 


- We can use the program `findsmb` to find samba shares on the network, basically machines on the network that might correspond to **SMB** probes on the network. 

- since in this example we used the line in `smb.conf` to contain only locally saved credentials, we have to make a password for a user in samba.
**That user must already exist as a user in the Unix OS iteslf**

Try running `smbpasswd -a vader` then enter the SMB password for user vader to set the password for vader

- To view the shares, use the smbclient program as `smbclient -L PLURAL-SERVER -U vader` the -L is list on the net-bios server name with the user vader.
Then enter vader's password to see the share! 

> We see the docs share which defined and also a share called IPC which is Inter Process Communication and is something Samba uses internally , also we see the server name and the workgroups

- Finally, to connect to the server `smbclient //MAMBA-SERVER/docs -U vader` 

> will enter the smb: \> command share prompt

This is similar to the FTP shell, so you can `get` and `put` files and also quit 

- To see the samba status of what's using what, use `smbstatus` to see the pid's, service files etc. 

- To connect / view the share on a windows on the same network, 

  The `MAMBA-SERVER` is listed in the Network!
  We can see the docs machine and also have access to this share!
  You'll most likely have to enter the credentials for the SMB share i.e those for Vader) 

 
> There's also a  web administration tool called SWAT Samba web Administration Tool, but it's not supported in Samba 4, so I didn't learn much about it which is why I can't write about it. But you might come across something like this on legacy or older systems
