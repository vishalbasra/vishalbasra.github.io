---
layout: post
title:  "Knife Handy Commands"
categories: Chef

categories:
  - Chef
tags:
  - Chef
  - Cheat-sheet

excerpt: A chef must have his/her knife!
---

What is knife?

**[Knife](https://docs.chef.io/knife.html)** is what let's us talk to the Chef server.

## Knife Handy Commands


| Acronym        | Explanation  |
| ------------- |:-------------:|
| `knife node list`      | Show all nodes |
| `knife --version`      | Show the version, lol |
| `knife cookbook create <bla>`      | Makes a Cookbook |
| `knife node run_list add web1.awesomehost.com "receipe[apache]"`      | Makes to run list |
| `knife node run_list remove web1.awesomehost.com "receipe[apache]"`      | Removes from run list |
| `knife client list`      | Show all clients.[Also see Clients vs Nodes](#clients-nodes) |
| `knife node show <nodename>`      | Show the details of the node-name |
| `knife node edit <nodename>`      | Edit the node9object) - edit the runlist |
| `knife search node 'recipes:cookbook_name\:\:recipe_name’`      | Show nodes which have the `recipe` of the `cookbook` attached |
| `knife search <query> -a <attribute name>`      | Seach for nodes with the attribute name |
| `knife search 'os:windows AND name:*.awesomehost.com* AND !region:production' -a "my.attribute"`      | Seach with a query and attributes |
| `knife winrm --winrm-user 'domain\username' 'hostname:host-001.awesomehost.com' 'net statistics server'`      | Knife `WINRM` example |
| `knife ssh 'hostname:host-002.awesomehost.com' 'hostname'`      | Knife SSH example 1  |
| `knife ssh --ssh-password <plaintext> 'id:01 AND os:linux AND name:host-002.awesomehost.com' 'cat /proc/sys/vm/swappiness'`      | Knife SSH example 2 |
| How to see multiple run-lists    | [Run-List for multiple nodes](#runlists) |
| `knife data bag list databag-ssl-certificates`      | See stuff in `databag-ssl-certificates`  |
| `knife data bag show databag-ssl-certificates`      | Show stuff in `databag-ssl-certificates` |
| `knife data bag show databag-ssl-certificates Awesomehost-RootCA`      | See stuff further down in a databagg `databag-ssl-certificates` |
| `knife environment compare dev staging`      | Compare two environments |




## See Run-Lists of multiple nodes ##
{: id="runlists"}

This can be used to
- see order of cookbooks on the nodes
- other interesting stuff on the run-lsit itself of the node!

| S.No        | Action  | Command  |
| 1.     | Get list of nodes you want to query on |    `knife search node "chef_environment:testing" -i > node-list.txt`   |
| 2.     | Make a directory |    `mkdir run-lists`   |
| 2.     | Save the run-lists of nodes you want to query on |    `for i in `cat node_todo.txt` ; do knife node show -F json $i > run-lists/$i.json ; done`   |
| 3.     | Do `bash` magic eg; to see order of cookbooks linux and windows after `cd run-lists` |    `for i in `ls -l \| grep -v './'   \| awk '{ print $9 }'` ; do echo $i && grep -ni 'linux' $i && grep -ni 'windows' $i ; done > /tmp/order.txt`   |


## Client VS Node ##
{: id="clients-nodes"}

A `client` -  is a user that will authenticate against the Chef server to do something
A `node`   -  is a machine that uses data from the Chef server to configure itself.


A `client` will have the level of permission to access data from the Chef server
A `node` will get the list of recipies / cookbooks to be run

 
Consider a webserver `web1.awesomehost.com` , when it connects to chef-server to get all the recipies needed for `web1.awesomehost.com` it says "Hello Chef Server, I am client `web1.awesomehost.com` and here, I can authenticate to you, I need the `recipies` / `run-list` / `node-object` etc. for the `node` `web1.awesomehost.com` please pass them along.
