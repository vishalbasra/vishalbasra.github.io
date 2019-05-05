---
layout: post
title:  "Samba File Shares"
excerpt: Understanding File Shares in Samba!
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

# What does this post entail ?

##  How user accounts are managed on the stand alone samba server ?

  - This begs the question, what is a `stand-alone` server w.r.t Samba ?
  > It's a server that doesn't depend on any other server to store user account infornmation or perform authentication and does both of these by itself thus storing things locally. This further begs the question (too much begging going on here) how can the account information be stored properlly, locally?

<div align="left">
Multiple ways to do that <br>
    i.) The traditional storage location file is a flat file called  <code>/etc/samba/smbpasswd</code> , this is best regraded as obsolete and is analogous to the <code>/etc/passwd</code> flat file but is still supported<br>
    ii.) There is also <code>tdbsam</code> <b>Trivial DataBase SAM</b> , is bundled with Samba, is easy to use and requires no configuration to work<br>
    iii.) There is also <code>ldapsam</code> uses an external directory service <br>
    iv.) <code>mysql</code> can also be used! <br><br>

  Option iii.) and iv.) are the most scalable options, but need additional configuration and schema to be made and maintained.

from within `smb.conf` this is how we can define which password storing method should be used in the <code>[global]</code> block<br><br>
</div>

<div align="left"><pre><code>
[global]
  passdb backend = tdbsam: /etc/samba/private/passdb.tb
</code></pre></div>

So we see that `tdbsam` is the default and the default filename for the database is `passdb.tb`

If this line is left out, the defaults will be assumed, so we may skip configuring this line all together.
The `private` directory identified by `PRIVATE_DIR` is something that is compiled into samba and may be changed after another compilation.

 <b>Question :</b> Linux uses it's own methods to store account information like the `/etc/password` or the ness / nss or ldap so why doesn't samba use that and what's the need to have a seperate set of place to store credentials?

 > Modern windows systems authenticate for CIFS by sending an encrypted password and the encryption algorithms that windows use are different from the algorithms that linux uses so the hashes are useless.


  - Security Levels<br>
      - Share level ( really an old way of doing it ) - basically it protects a share with a password being associated with a share
        <br />Set as
          - `security = share` ( very bad way, please don't do it! )

    - User Level Security - a password is associated with a user  
      Set as
        - `security = user`  means it's a stand alone server, also this is a default setting
        - `security = server`  samba will delegate authority to another CIFS server ; kind of obsolete
        - `security = domain`  samba will authenticate via the NT doain controller
        - `security = ads` samba will authenticate using the Active Directory Domain Services ( probably the best way to implement it right now )



  - How to interact with the accounts, `smbpassword` is the program that's used to interact with accounts!

     - a '-a' means add, so if you do `smbpasswd -a vader` it'll fail because vader does not have a linux user ID account. Thus, In this way(stand-alone) samba requires the users to be existing as a linux UID first.

  - To look at the `TDB` database file directly isn't possible because it's a binary file, but the file can be viewed with a program called `pdbedit`
    - Use it like this `pdbedit -Lw vader`
    - The format of the file is like`username:LinuxUID:LAN manager password hash: windows NT password hash: <some flags like U meanning a regular user account> : time of the user creation`

  - All this is controlled and managed in the `TDBSAM` database and that database's file is controlled by the 'PRIVATE_DIR' and to check that, run
    - `smbd -b | grep -i private` and check the value of "PRIVATE_DIR"
    - "PRIVATE_DIR" has two files `passdb.tdb` and `secrets.tdb` , when a windows client logs in with it's encrypted password, the hashes are compared.
    - Thus this passwords are called 'plain-text equivalent passwords' meanning if an attacker get's the encrypted hash, it's as good as done.

## How to control access to the shared shares?

Now that we know these details, we need to control who can access the samba service and it can be accessed at several points.

  - These ports need to be opened ( if there's a packet-filtering firewall, ensure that these ports are enabled )
      - port `137` - UDP NetBIOD name service
      - port `138` - UDP NetBIOS datagram service
      - port `139` - TCP NetBIOD session service
      - port `445` - TCP CIFS


  - Ensure the  Access control rules within the samba server in `smb.conf`

    - Example of access control within a share
```
[demo]
  path = /home/demo
  hosts allow = 127.0.0.1 192.168.1.0/24
  valid users = @admins, james, mary
  read only = yes
```
    - control based on IP, IP ranges, user-names, user-group membership

    - The `hosts allow` is one of many directives, some of them are :-

      - `hosts allow`      list of hostnames, ip addresses or address blocks which are allowed to connect
      - `invalid users`    lists of users who cannot access the share, this OVER RIDES the hosts allow list
      - `read list`        lists of users who have read-only access to an otherwise writable share
      - `write list`       Users who have read/write access to an otherwise readable share
      - `admin user`	     Users who can access the share as root


    - These can be set in the `[global]` section and also be set in individual shares themselves.


  -  The linux file system also has the very  important underlying file system preferences! These will also over-ride who can do what and where!


##  How to export home directories ?

If there are a lot of users and you want to share their home directories, then there would be a lot of entries to make in `smb.conf` and then also smb.conf will
also have to be modified a lot number of times.
Also there will be a lot of enteries and access controls that'll have to be altered, modified.

To make life simpler, samba supports a `[home]` service defination which creates shares on the fly to provide access to a user's home directory

```
[homes]
  browsable = no
  writable = yes

```

 > Good thing to know, browsable and writable can be written with or without an e

Here's how this works :-

  - i) Someone makes a call to connect to a samba share called 'alice'
  - ii) Samba will check if there's already a share called 'alice' , if yes, it'll connect to it.
  - ii). If not, samba will check to see if there is a `[homes]` section in smb.conf, if not the connection will fail.
  - iv) If yes, samba will check to see if there's a linux user account with the username 'alice', if yes, it'll check that if the password supplied while attempting a connection is the same as that in the linux /etc/passwd file(could be somewhere else also these days), if yes for both these questions, it will lookup's alice's home directory, make a share and connect.
  - v) If not, it'll disconnect.


## Logging in the mamba!

By default samba doesn't log it's action like a middleware, like Apache does.

By default, samba logs minimal logging activity.
Most likely if you up the amount of stuff that's logged, you'll be debugging something.

- To control logging, use the `log level` directive in `smb.conf`
- The default log level is `0`, the maximum is `10`
- Most sys admins use a max level of 2/3. Anything higher will print a lot of information.
- Most samba developers use levels above this to debug samba code.

- How to alter the log level?

```
[global]
  log level = 2
  log level = passdb:5 winbind:2
```

Thus the log level can be specified for each "debug class"(features within samba) , which are -

 - `all`
 - `tdb`
 - `smb`
 - `auth`
 - `winbind`
 - `passdb`
 - `quota`
 - etc. etc.

The log file name is also printed in the log file itself

- The log file names can be specified in the `smb.conf` itself using the `log file` directive , use it like this

```
log file = /var/log/samba/log%m.log
```

This will ensure there are seperate log files made!

This is a typical example of the `log file` directive.

  - Notice the `%m` is a macro which allows variable substitution based on the client machine name, user identiry, share name, any kind of variable really.

  - These can also be seen in `/var/log/syslog.d

Some other common macros include :-
```
- %U    Session username ( the username with which the client is connected to the samba server )
- %G    The primary group of the user
- %m    The NetBIOS name of the client machine
- %L    The NetBIOS name of the server
- %M    The internet name of the client machine
- %a    The architecture of the client machine
- %T    The current date and time  
```

For a full list, please see "*VARIBLE SUBSTITUTION*" in the official man page of `smb.conf` / samba


## Using a linux machine as the client in a linux and windows environment ( how to see windows exported shared on a linux box )

**Use Case** Trying a windows file server shares to be mounted on linux.

- You don't need samba to do it, the support to mount CIFS shares, is build into the linux kernel.

- `Linux FS vs Win FS` (miniature 101)

  - Linux uses many different types of FS like ext3, ext4 , btrfs etc.
  - But Linux also supports the POSIX file system semantics
  - File names are case preserving and case sensitive.
  - Only illegal character in filenames is "/"
  - File Permissions are different , rwx
  - file ownership represented by a single UID eg: 504


 - Most mordern `Windows operating systems use` some form of `NTFS` ; `New Technology File System`
  - File systems are case preservice but not case sensitive. ( This begs the question if foo.txt and FOo.Txt are in a linux FS which is shared via samba and connected to a windows system, how will windows display it? Well windows will display whichever file comes first lexically.)
  - Many different illegal characters ( approx 8 of them though this could change in the future )
  - Permissions are differnet
  - File ownership is represented by a number called SID , Security Identifier which is a big ass number



( Ofcourse you don't have to type the password, the OS will prompt you for it.)

```
mount -t cifs //Foo/bar /demo \ -o username =vader,password=mypassword
```
    - `//Foo/bar` is the `UNC name`    
    - `/demo` is the local mount point name

This in turn calls the `mount.cifs` function / program to do the actual work.


  - For all options, have a look at the man page for mount.cifs
  - Eg, this can be done :-
```
mount -t cifs //Foo/bar /demo \ -o username =vader,password=mypassword,uid=vishal,gid=vishal,filemode=0644
```

Ofcourse saving the credentials or even using them from the command line is a rahter dumb, stuid thing to do.

So you can do this

`vi /myfilename`
```
username=vader
password=iamyourfatherluke
```
then do this :-

```
mount -t cifs //Foo/bar /demo \ -o credentials = /myfilename,uid=vader,gid=vader,filemode=0644
```

This will also work and this entery can be made in the fstab file.
The fstab syntax is  

`//UNC MOUNT PATH /LOCAL MOUNT POINT TYPE(OF FS) OPTIONS`

So this becomes

```
//Foo/bar /demo cifs credentials = /myfile,uid=vader,gid=vader,filemode=0644
```
