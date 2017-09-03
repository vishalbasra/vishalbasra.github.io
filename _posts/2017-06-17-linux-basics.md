---
layout: post
title:  First Article!
excerpt_separator:  <!--more-->
categories:
  - Linux
tags:
  - general
  - code
  - Linux
  - bash
---

Hello world, foo-bar!

This is my first post, enter obligatory hello world. 
But the idea of writing a lame ass post would just be awkward, which is what I percived.
<center>So,</center>
I came up with the idea to seggregate the website into categories and the first one that came to my mind, was of-course Nix. 

The idea of this post is to be :- 
- An obligatory hello world (as I've mentioned)
-  My first post to test out the site's functionality
- My first post to test out the SASS (lol)


What better / quick way to write a small script to achieve these goals!

Since i couldn't think of anything better to do, here's what this does, SSH's in, get's the status of a process, checks it and logs if the processes are running.

Here goes nothing

{% highlight bash %}
#!/bin/sh

function remote_shell {

remote_output=$(ssh $2@$1 -i $6 ps aux | grep $3 | grep -v 'grep')

if [[ $remote_output == *"$4"* ]]; then
  echo "$(date) | $5 is running" >> /var/log/$6/$5.log
else
  echo "$(date) | $5 is not running" >> /var/log/$6/$5.log
fi

}

if [ -d /var/log/$6 ]; then
  :
else
  mkdir -p /var/log/$6
fi

#examples of how this can be used 
remote_shell $1 $2 named /usr/bin/named named /opt/somefolder/ssh_keys/myuser_private_ssh_key my-log-location
remote_shell $1 $2 rsyslog /sbin/rsyslogd Rsyslog /opt/somefolder/ssh_keys/myuser_private_ssh_key my-log-location

# ---- How to call this script ----
# sh remote_process_check.sh my-server.mydomain.com myuser
# good idea to CRON it ( preferably anacron )

#    $1 server name
#    $2 username
#    $3 grep string
#    $4 search string
#    $5 log file name
#    $6 path to SSH key
#    $7 location within /var/log directory where the script should log

{% endhighlight %}

