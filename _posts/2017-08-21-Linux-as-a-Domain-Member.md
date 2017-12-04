---
layout: post
title:  "Linux in AD"
categories: samba

categories: 
  - Linux
tags:
  - Linux
  - Samba
  - AD
  - Concept

excerpt: Make a Linux machine a respected memer of the AD community!
---

# Linux machine as a domain member 

Configure SAMBA as a domain member server to get unified user accounts for windows and linux!


This will entail the use of four technologies 

1. `NSS` **Name service Switch** , this is how linux figures out how to do name resolution 

2. `PAM` **Pluggable Authentation Modules** , this is how linux figures out how to do authentication 

3. `KerberOS` which is the authentication service used by Active Directory 

4. `Winbindd` daemon , the bridge between linux and windows. 


Winbind is the main protagonist of the linux AD story, but we need to understand how everything about this works to be able to appreciate winbindd. 

## NSS
  -  NSS `Name Service Switch` 

      - In the most of the common standard linux libraries there are a set of routiines called resolvers because they answer queries. 
      - The one's we are interested in are of the likes `getpwent() and getpwam()`
      - These will lookup a user account by name and then get the userID, home directory etc. 
      - These all consult a file called `/etc/nsswitch.conf` and since these are the resolvers for user details, these all look for a directives called `passwd` mentioned like this :- `passwd: files nis winbind` in the file
      - The names in the line above resolve directorly to library names, so 
        - `files` means that the `libnss_iles` library will be used which points to the file `/etc/passwd` 
        - `nis` means that the  `libnss_nis`  library will be used. `nis` used to be called yellow pages and might be thought of as a backend account information store 
        - `winbind` means that the `libnss_winbind` library will be used which means that the lookups will happen by consulting with the `winbindd` daemon

<br />
## PAM
  -  PAM `Pluggable Authentication Modules` is a framework within Linux that allows applications to do authentication related activities. 
 To help best understand PAM, consider this scenario 


     - There is a PAM-aware program like login ( or it could be the SSHD daemon ; a PAM-aware program means that any program that uses PAM to do authentication )
    - i) login uses a library called `libpam`
    - ii) The `libpam` directory consorts to a configuration file at `/etc/pam.d/<program name>` eg like at `/etc/pam.d/login/`
    - iii) Under the control of the enteries that `libpam` finds in that file, `libpam` will assemble it's four modules which combined will allow authentication to work , these are 
 sted below   - iv) Please note that the stacks of PAM modules are stored in `/lib64/security`

     - These are 
       - `auth`
       To put simply it does capture of a username and password and verifies it with the stored enteries to do authentication 
       - `account` 
      Account policies like account expiration, if an account is only allowed to be accessed during a specific time, etc. 
       - `password` 
      Password management , updating credentials, setting a new password!
       - `session` 
      Allocation of resources as a user logs in or establishes a session 

     - So read through it, but be aware that the `pam_winbind.so` module is used in all four stacks! 

     -   What's important to understand is that these are the modules that allows us to use `winbind` with the Windows AD world <br /><br />


##  KerberOS 

It's usually described as a **'trusted third party'** authentication service. 
Though 'Kerberos' is a highly vast subject and there can be a proper complete course on the subject.

- Goal is to provide **'Single Sign On'** by providing a way for the client and a server (known as `principles` in kerberos lingo) to securely authenticate one to the other. 
   This is made possible by the use and exchange of `encrypted tickets` 
- This technology originiated in 1980 from MIT as a part of Project Athena 
- it was adopted by MS for authentication in Windows 2000 and has been used ever since. 
- Why is it called kerberos, well kerberos was the name of the `Three headed dog` that guarded the entrance to the *Chamber of Hades*
- The user ( client which needs to do SSO )
- Kerberos Distribution Center (`KDC`) which performs two services 
    - i)  Authentication Service 
    - ii) Ticket Granting Service 

- The Application Server the machine that has the service for which authentication is being requested for - 
or the **Kerberos Authentication Process**

1) The user sends a `Request Ticket Granting Ticket` containing the username and password to the KDC. 
   This is the SSO operation as seen from the user prespective.<br />
2) The `Ticket Granting Service` or `TGT` in the `KDC` will respond with a `TGT` or a `Ticket Granting Ticket`<br />
3) Some time later, user wants to access some application 
   In the background, her client machine will issue a `Request Service Ticket` for that service. 
   Included in `RST` will be a copy of `TGT` as a proof of entitelment to the `SGT` or `Service Granting Ticket`<br />
4) Kerberos will send a `Service Ticket` for the application <br />
5) The client will then present the `Service Ticket` to the application server and demand service, the Application will authorise the client and grant the service. <br />


- The applications that take a part in the exchange of the Tickets like the `User Machine` and the `Application Server` are called the `Principals` 

- The whole complete thing is known as a `Kerberos realm`


##  Winbindd 

This is a daemon that acts as a brigde between linux and windows operating systems to provide these services - 

- user account information 
- authentication 

On the Linux side, it listens to the unix domain socket `/var/run/winbindd/pipe`

On the windows side, it listens to 
  - RPC protocol : `Remote Procedure Call` protocol to talk to the NT Domain controllers ( almost obsolete ) 
  - and to LDAP, KERBEROS and DNS to talk to the Active Directory Domain Controller

A major challenge for winbindd is the `User ID's` 

The linux world needs usable UID's but the windows world has SID's ( Security Identifiers ) and there's no obvious way to map from a SID to a UID 

So `winbindd` simply allocated a UID to an SID from a set of range of UID's and then remembers these connections fro UID's to SID's in a `cache` so that next time it returns the same linux UID

User account information in linux looks like this 

```
<username>:<encrypted password>:<userid>:<groupid>::/<home directory>:<shell> 
```

If you don't know what `enumerate` is, it means to list!


Configuration options for winbind are in `smb.conf` and some of the useful ones are like - 

- `winbind cache time`		- Number of seconds to cache user and group info 				600 
- `winbind enum users`  	- Allow enumeration of user accounts 						yes ( default is no )
- `winbind enum groups`		- Allow enumeration of user groups 						yes ( default is no ) 
- `template homedir`		- Template for users home directory 						/homer/%U ( This is again a macro variable)
- `template shell`		- Template for users shell   							/bin/bash
- `winbind use default domain`  - Treat usernames that lack a domain as part of the default domain 	 	yes
- `winbind uid` 		- Range of values to allocate UID's from 					2000 - 3000
- `winbind gid`			- Range of values to allocate GID's from 					2000 - 3000


# Joining a Domain!

- Please ensure that Netowrk Manager is not running, as it will write System network files! 

`service NetworkManager off`

`chkconfig NetWorkManager off`

- configure the networking device with the basic settings like these directives DEVICE, ONBOOT, BOOTPROTO ( DHCP is off ),IP ADDR,NETMASK 

- restart the network service 

- Change the resolver nameserver and the search domain to the AD server! 

- Check the linux system clock, because kerberos uses the timestamp as a part of it's protocol and if the clock on the client / server is too skewed then kerberos will fail 

  So ensure that the date, time are running fine, try starting the `NTPD` daemon! 


1. Configure Kerberos 

Assume that the domain name is called vader.local

- Open the file at `/etc/krb5.conf` 

```
[libdefaults]
  default_realms = VADER.LOCAL
# the convention is that this is the same as the domain name and is in upper case 

[realms]
VADER.LOCAL = {
  kdc = domaincontrollerhostname.vader.local # this should be resolvable 
  default_domain = vader.local
}

[domain_realm] # showcase mappings between domain names and realm names 
  .vader = VADER.LOCAL
  vader = VADER.LOCAL

```

- Post this you'll be able to get a `Request TGT` and you can check by ( kinit also has a -v verbrose option ) 
`kinit Administrator@VADER.LOCAL`<br> 
The Administrator is an admin level account on the DC on the domain 

- No error messages or no messages means `TGT` was sucessfully received 

- To see all tickets, check using `klist` ; each ticket will have a principal , user , validity , expiration , service principal etc. 
You might see `krbtgt` as the Service Pricipal in kinit which means that the ticket was gotten by the Kerberos Ticket granting ticket service 


<p>2. Configure Samba </p>

i. `/etc/smb.conf` sample 

```
[global]
  netbios name = CENTOS6SERVER ( should be FQDN ) 
  workgroup = any example 
  name resolver order = wins bcast 
  idmap config * : backed = tdb 
  idmap config * WORKGROUPNAME: backend = ad
  idmap config * WORKGROUPNAME : range = 700-800 
  log level = 2 
  log file /var/log/samba/log
  securiry = ads # this mean active directory services 
  realm = VADER.LOCAL 
  winbind uid = 2000-3000
  winbind gid = 2000-3000
  template shell = /bin/bash
  template homedir = /home/%U
  winbind use default domain = true

```

ii. Join the domain with an account who has the provilage to join the domain! 

- `net join -U Administrator` # might have a higher debugging option which can be used as a '-d2' flag possibly, consult man. Alternatively check the samba log file 
- Then test the join as `net ads test join` 


<p>3. Start winbind and ensure that it loads on bootup</p>

- `service winbind start` 
- `chkconfig winbind on`
- Verify using a dagnostic tool called `wbinfo` which is a part of samba 
  - `wbinfo -D vader.local` to see domain information 
  - `wbinfo -u` will show local users and also the AD users 
  - `wbinfo -g` will only list groups in AD 

- To verify that winbind is working for user authentication  `wbinfo -a username` <br>
The `a` stands for authenticate and the username is a user in AD
- It'll ask the password twice and then check the challenge / response. 


<p>4. Now we need to let PAM know about winbind</p>

- Ofcourse this can be achieved manually by editing the PAM config files, but here's a little tool called `authconfig`
- But if you messup PAM, you could lock yourself out! ( and it's very easy to do so even if you aren't stupid like me! ) so keep a login session alive as root from somewhere 
- Define user Information ( where it will come from ) 
- Define Authentication ( winbind and local ) 
- This will change `/etc/pam.d/system-auth` and under the auth module lots of things will change eg, will contain `pam_winbind.so` and `pam_unix.so` will be used 
- The file `/etc/pam.d/system-auth` is used in most PAM aware programs like login, sshd etc. 
- Once logged in use the `id` command to check the uid and gid :) 

<p>5. Storing linux account infromation in active directory (optional)</p>
 
- Starting in windows server 2003, MS started support to store linux account info in AD like UID, GID, home dir etc. 
- These are extensions to the LDAP schema and are defined in `RFC 2307` 
- If winbind is running independently, then they are going to use independednt UID's for the same user in Active Directory meanning for the same SID
- To add these information in active directory, then we can access these from winbind on a linux system. 

i. Install some extra features 
Deployment Image Servicing and Management `dism` 

```

# install Adminui feature 
dism /online /enable-feature /featurename:adminui /all

# install a feature called NIS 
dism /online /enable-feature /featurename:nis /all

# This is not the same as the nis in linux 
# (The linux `nis` is the one which is called yellow pages )
```

ii. Go to Active Directory users and computers > add a new user ( standard stuff )

iii. Now we see a `UNIX attributes` tab! 
     Specify the NIS domain , UID , default shell , home directory , primary GID 

iv. In `/etc/smb.conf` then ensure that two lines are present as such 

```
under global 

idmap config DOMAINNAME : backend = ads
idmap config DOMAINNAME : range = 700 - 800 
```

# Until NOW users will have a problem that their home directories will NOT  have been created , to fix 

i) This can be fixed from the `pam winbind` module

ii) Open `/etc/pam.d/system-auth` 

iii) In the `session` stack add the following line 

Note that the `optional` key word does not mean that the module might or might not get invoked, it only means that it doesn't matter if the module was sucessful or not.

```
session   optional  pam_winbind.so mkhomedir
```

Also note that the module name is `pam_winbind.so` and the paramter that we're passing to the module is `mkhomedir` which means make home directory on the fly of-course.

This will likely need SELinux to be disabled too! 

# Now the linux OS is in the domain and you can even see the linux user options AD!
