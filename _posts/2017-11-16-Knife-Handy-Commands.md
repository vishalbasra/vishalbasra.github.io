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
| `knife node show <nodename>`      | Show the details of the node-name |
| `knife node edit <nodename>`      | Edit the node9object) - edit the runlist |
| `knife search node 'recipes:cookbook_name\:\:recipe_name’`      | Show nodes which have the `recipe` of the `cookbook` attached |
| `knife search <query> -a <attribute name>`      | Seach for nodes with the attribute name |
| `knife search 'os:windows AND name:*.awesomehost.com* AND !region:production' -a "my.attribute"`      | Seach with a query and attributes |
| `knife winrm --winrm-user 'domain\username' 'hostname:host-001.awesomehost.com' 'net statistics server'`      | Knife `WINRM` example |
| ` knife ssh 'hostname:host-002.awesomehost.com' 'hostname'`      | Knife SSH example 1  |
