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
| `knife <command> --help`      | List out any help eg: `knife node --help` |
| `knife node list`      | Show all nodes |
| `knife --version`      | Show the version, lol |
| `knife cookbook create <bla>`      | Makes a Cookbook |
| `knife node run_list add web1.awesomehost.com "receipe[apache]"`      | Makes to run list |
| `knife node run_list remove web1.awesomehost.com "receipe[apache]"`      | Removes from run list |
| `knife client list`      | Show all clients.[Also see Clients vs Nodes](#clients-nodes) |
| `knife node show <nodename>`      | Show the details of the node-name |
| `knife node show -l -F json <nodename>`      | Show the all details, including Attributes in JSON |
| `knife node edit <nodename>`      | Edit the node9object) - edit the runlist |
| `knife search node 'recipes:cookbook_name\:\:recipe_name'`      | Show nodes which have the `recipe` of the `cookbook` attached |
| `knife search <query> -a <attribute name>`      | Seach for nodes with the attribute name |
| `knife search 'os:windows AND name:*.awesomehost.com* AND !region:production' -a "my.attribute"`      | Seach with a query and attributes |
| `knife winrm --winrm-user 'domain\username' 'hostname:host-001.awesomehost.com' 'net statistics server'`      | Knife `WINRM` example |
| `knife winrm --winrm-user 'domain\username' 'hostname:host-001.awesomehost.com' 'schtasks.exe /QUERY /TN "chef-client"'`      | Knife `WINRM` example 2 |
| `knife winrm --winrm-user 'domain\username' 'hostname:host-001.awesomehost.com' 'schtasks.exe /CHANGE /TN "chef-client" /DISABLE'`      | Knife `WINRM` example 3 |
| `knife ssh 'hostname:host-002.awesomehost.com' 'hostname'`      | Knife SSH example 1  |
| `knife ssh --ssh-password <plaintext> 'id:01 AND os:linux AND name:host-002.awesomehost.com' 'cat /proc/sys/vm/swappiness'`      | Knife SSH example 2 |
| How to see multiple run-lists    | [Run-List for multiple nodes](#runlists) |
| `knife data bag list databag-ssl-certificates`      | See stuff in `databag-ssl-certificates`  |
| `knife data bag show databag-ssl-certificates`      | Show stuff in `databag-ssl-certificates` |
| `knife data bag show databag-ssl-certificates Awesomehost-RootCA`      | See stuff further down in a databagg `databag-ssl-certificates` |
| `knife environment compare dev staging`      | Compare two environments |
| `knife search node "chef_environment:awesome-env AND os:linux" -i`      | See all nodes in your env |
| `knife status 'env:production AND name:*awesome*' -r`      | See nodes in search query with last chef-runs and the run-list |
| `knife configure client`      | Configure  `client.rb` |
| `knife configure`      | Configure  `knife.rb` |
| `knife bootstrap <nodename> -x <username> -P <password> -N <nodeName>`      | Bootstrap Bootstrap |
| `knife cookbook create <cookbookName>`      | Create a cookbook |
| `knife cookbook list`      | List all the cookbooks |
| `knife cookbook delete <cookbookName>`      | Delete a cookbook |
| `knife bootstrap <whatever options>`      | Bootstrap options |
| `knife cookbook site <whatever options>`      | Cookbook options via/for the supermarket |
| `knife search node 'name:awesomehost-01 AND virtualization_system:vmware`      | Search nested attributes within the actual search query |
| `knife data bag from file idname.json --secret-file encrypted_data_bag_secret`      | Create databag or databag item from file and encrypt it |





## See Run-Lists of multiple nodes ##
{: id="runlists"}

This can be used to
- see order of cookbooks on the nodes
- other interesting stuff on the run-lsit itself of the node!

| S.No        | Action  | Command  |
| 1.     | Get list of nodes you want to query on |    `knife search node "chef_environment:testing" -i > node-list.txt`   |
| 2.     | Make a directory |    `mkdir run-lists`   |
| 2.     | Save the run-lists of nodes you want to query on |    `for i in `cat node-list.txt` ; do knife node show -F json $i > run-lists/$i.json ; done`   |
| 3.     | Do `bash` magic eg; to see order of cookbooks linux and windows after `cd run-lists` |    `for i in `ls -l \| grep -v './'   \| awk '{ print $9 }'` ; do echo $i && grep -ni 'linux' $i && grep -ni 'windows' $i ; done > /tmp/order.txt`   |

## Update run-lists of multiple nodes ##

This can be used to
- Update the run-lists any subset of nodes
- I'm only appending a cookbook to the end of the run-list but this can be modified a custom use-case
- Get list of nodes you want to perform the action on - `knife search node "chef_environment:testing" -i > node-list.txt` or `grep` from `knife node list | grep 'myname' > /node-list.txt`
- Add your cookbook to the run-list for all - `for i in $(cat /tmp/node-list.txt); do knife node run_list add $i 'recipe[my-code]' ; done`




## Client VS Node ##
{: id="clients-nodes"}

A `client` -  is a user that will authenticate against the Chef server to do something
A `node`   -  is a machine that uses data from the Chef server to configure itself.


A `client` will have the level of permission to access data from the Chef server
A `node` will get the list of recipies / cookbooks to be run


Consider a webserver `web1.awesomehost.com` , when it connects to chef-server to get all the recipies needed for `web1.awesomehost.com` it says "Hello Chef Server, I am client `web1.awesomehost.com` and here, I can authenticate to you, I need the `recipies` / `run-list` / `node-object` etc. for the `node` `web1.awesomehost.com` please pass them along.

## Change vagrant stuff in kitchen ##
All right, this is not a knife thing, but you may ant to see **[this link](https://github.com/chef-cookbooks/jenkins/blob/f4ecce7152011d2252d7c34697be211315b51b3c/.kitchen.yml#L6-L11)** to set all sorts of stuff for Vagrant in your kitchen file.

Here's extracted code from that page for reference 
```
driver:
  name: vagrant
  customize:
    cpus: 2
    memory: 1024
  network:
    # Allow access to the Jenkins webui which is useful when troubleshooting
    - - forwarded_port
      - guest: 8080
        host: 9090
auto_correct: true
```
