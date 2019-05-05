---
layout: post
title:  "Samba 101"
excerpt: Getting started with Samba, or Samba 101!
excerpt_separator:  <!--more-->
categories:
  - Linux
tags:
  - Linux
  - Samba
  - AD
  - Concept

permalink: /:categories/:title/

---
# Getting Started with Samba!
---


<br />I'm writing this post with the idea of how I first learned about samba, and I intend to write this post as a reference for me or for someone to read it and understand the key concepts.
This is not going to be a details step by step technical walkthrough, rather a post that'll work to help better understand the key concepts.

But before we can delve into Samba and how it works, it's important to understand

## The most shortest history of Windows Networking wrt this post

<div align="left">
1. In 1985,
<br>Microsoft didn't support TCP, so they made <code>SMB</code> or <code>Server Message Block</code> work over something called the <code>NetBIOS</code> protocol.<br>
The NetBIOS protocol was MS's solution at the time to do a couple of things ( more details later )<br><br>


SMB at this time :-<br>
  a. supported file sharing within workgroups<br>
  b. supported printer sharing within work groups<br>
  c. ran over NetBIOS and NBF - ( <code>NetBIOS Frames</code> )<br>
  d. identified machines by a NetBIOS name - yes, MS did not use the now standard <code>DNS names</code> lol<br>
  e. machines had no underlying numeric address ( so there was no need for name resolution )<br><br>

The NetBIOS protocol was not routable and was intended for a bunch of machines on the same network.  
Soon NetBIOS was encapsulated over TCP ( MS learned it was useful to do what everyone else was doing)<br><br>


2. In 1987,
<br>Microsoft learned to encapsulate NetBIOS traffic over TCP and called it  <code>NBT</code> - <b>NetBIOS over TCP</b><br>

Name assignment :  (NetBIOS name -> IP address )<br>
Name resolution :   handled in a <code>lmhosts</code> file , this is analogous to the <code>/etc/hosts</code> file<br>
Also :<br>
   - Central name service called <code>WINS</code><br>
   - Broadcasting<br>
   - Machines could register their NetBIOS name and IP address to WINS<br><br>


3. Meanwhile in 1996,<br>
SMB continued to evolve and in 1996, it was rebranded as <code>CIFS</code> or <code>Common Internet File System</code><br>

- It ran directly over TCP / IP<br>
- NetBIOS was not used anymore<br>
- The old NetBIOS way of name resolution was changed to give way to DNS name resolution<br><br>

4. Back in 1993,<br>
Windows NT introduced a concept of <code>domain</code><br>

- It's a set of resources protected via a single network wide login, it's intended to be used as a SSO for a single network<br>
- There's a Primary DC which holds authoritative account information<br>
- There's also a Backup DC which holds copies and is in use to ensure availability<br><br>

5. In 2000,<br>
MS completely replaced the NT style domains with <code>Active Directory Domains</code><br>

These were similar in spirit with the NT domain, but certain things were different</br>

- These replaced the PDC and BDC roles with multi-master replication<br>
- Used technologies like :- ( these combine to make <b>Active Directory</b> )<br>
  - <code>LDAP</code> as the directory service<br>
  - <code>KerberOS</code> for authentication<br>
  - <code>DNS</code> for name resolution<br><br>

6. In further versions of windows servers,<br>
The <code>Domain controller</code> role was named to <code>Active Directory Domain Services</code> via the <code>Active Directory Domain Controller</code> role in servers 2003, 2008, 2012 and so on.

The <code>Active Directory Domain Controller</code> is one of the roles that that Samba is able to assume.
</div>
## Samba Mamba!
<div align="left">
1. Samba was named after SMB and was created by an Australian programmer called <a href="https://en.wikipedia.org/wiki/Andrew_Tridgell">Andrew Tridgell</a>  with <a href="https://en.wikipedia.org/wiki/Jeremy_Allison">Jeremy Allison</a>, based on the reverse engineering of the SMB protocol using a packet sniffer.<br>
  - Development started in 1992, and it's now a project with ~ 2 dozen active contributors at <a href="https://www.samba.org">Samba</a><br>
  - GNU General Public License<br>
  - no Client Access Licences ( CALs )<br><br>

2. Samba Roles<br><br>

- Standalone roles<br>
  Samba does not rely on any other machine to store account info or the authentication and can do<br>
  - File Server<br>
  - Print Server<br>
  - WINS ( Almost obsolete )<br><br>

- For windows domains, samba can do<br>
  - Domain Member Server<br>
  - windows NT PDC ( historical interest, is otherwise obsolete )<br>
  - Active Directory Domain Services ( happened in *samba 4* ) - this is the current main DC role that MS calls<br>
</div>

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


## Installing Samba sample

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

<div align="left"><pre><code>
[global]
  netbios name = MAMBA-SERVER
  security = user # this means that authentication will happen based on credentials that are locally stored on the server itself.
  name resolve order = wins bcast # this might not be absoulte necessary, but again this keeps with traditions
  # if this is given like this, then we have explicitly removed DNS


[docs] # This is the name of the share
  comment = documentation # maybe this is called comment
  path = /usr/share/doc
  read only = yes
  # or it can be called writable = no with the same effect
</code></pre></div>

<br>

> The traditional NFS UNIX file sharing is different, in NFS the name which the clients see is exactly the path in the server but in Samba we can choose a different name, like the share name is 'docs' but the path is different.

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
<br><b>That user must already exist as a user in the Unix OS itself</b>

Try running `smbpasswd -a vader` then enter the SMB password for user vader to set the password for vader

- To view the shares, use the smbclient program as `smbclient -L MAMBA-SERVER -U vader` the -L is list on the net-bios server name with the user vader.
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
