---
layout: post
title:  "Rolling Back the kernel"
categories: Linux

categories:
  - Linux

tags:
  - Linux
  - code
  - bash


excerpt: Roll back RHEL / Centos systems!
---

# I love awesome scenarios wherein one needs to roll-back the kernel

Who doesn't love the [kernel](https://www.kernel.org/)!


But there are um,cases when you need to revert an awesome update or perhaps the update didn't go well!

Ideally since the kernel is loaded into memory, you should be able to delete the installed version(package) from the FS and still be okay.

Ideally, everyone should also be getting free cake.



We have our old friend [GRUB](https://www.gnu.org/software/grub/)


> There are multiple ways to achieve this, many of which may be more 'effecient'. This article is meant to show-case only one of the ways to do it, which I find easy to do.

## RHEL 6 / Centos 6 

We're gonna leverage [grubby](https://www.systutorials.com/docs/linux/man/8-grubby/ )

1. See the default kernel - `grubby --default-kernel`
example - `/boot/vmlinuz-2.6.32-696.18.7.el6.x86_64`

2. Verify what is the kernel version in use - `uname -r`
example - `2.6.32-696.el6.x86_64`

3. See the default kernel to the path of the kernel you want to boot to - `ls -l /boot/vmlinuz*`

4. Verify that the kernel to which you want to revert to exists 
example - `ls -l /boot/vmlinuz-2.6.32-504.el6.x86_64` 

5. Set the path as the new default - `grubby --set-default=/boot/vmlinuz-2.6.32-504.el6.x86_64`

6. Verify that the kernel set is different from the one in use `grubby --default-kernel ; uname -r`

7. `reboot`

8. Once the machine comes back on, verify that the kernel loaded to the path you specified by again checking `grubby --default-kernel ; uname -r`

9. Delete the version which you reverted from i.e the problamatic version `yum remove kernel` **but be careful** you want to only remove the version you don't need and by default yum should only remove it, but keep your eyes open.

10. `reboot` again and again verify that the problamatic version is gone and the correct version is in use and check `grubby --default-kernel ; uname -r` again

11. You're golden! But have a look at `/boot/grub/grub.conf` and `/etc/grub.conf`


## RHEL 7 / Centos 7 

We're gonna leverage [grub 2](https://wiki.gentoo.org/wiki/GRUB2) because that's the new standard in modern contemporary operating systems.

See also [GRUB 2 Shotgun](https://wiki.gentoo.org/wiki/GRUB2_Quick_Start)

1. See all available versions `awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg` 
Alternatively `rpm -qa | grep -i kernel`

2. See current version to which you boot to `grub2-editenv list` 
There will be numbers associated with the different kernerl versions, you'll want to leverage those out.

3. Change the default kernel `grub2-set-default NUM` like `grub2-set-default 4`

4. Verify you set the correct number `grub2-editenv list ; uname -r`

5. `reboot` 

6. Once the machine comes back on, verify that the kernel loaded to the path you specified by again checking `grub2-editenv list ; uname -r`

7.  Delete the version which you reverted from i.e the problamatic version `yum remove kernel` **but be careful** you want to only remove the version you don't need and by default yum should only remove it, but keep your eyes open.

8.  `reboot` again and again verify that the problamatic version is gone and the correct version is in use and check `grub2-editenv list ; uname -r`
Also `awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg` should return that only your intended version is available based on the kernel versions you removed 

9. See also `/etc/grub*` files
