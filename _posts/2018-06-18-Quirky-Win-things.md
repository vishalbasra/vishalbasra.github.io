---
layout: post
title:  "Quirky Win things"
categories: LinWindowsux

categories:
  - Windows

tags:
  - Windows
  - cmd


excerpt: Some quirky albeit nice windows things!
---

| Quirky thing        | Explanation  |
| ------------- |:-------------:|
| `schtasks.exe /QUERY /TN "chef-client"`      | Status of a scheduled task  |
| `schtasks.exe /CHANGE /TN "chef-client" /DISABLE'`      | Disable a scheduled task  |
| `schtasks.exe /CHANGE /TN "chef-client" /ENABLE'`      | Enable a scheduled task  |
| `wmic product`     | Show all installed packages  |
| `wmic product get /format:csv > Software_%Computername%.csv`     | Save all installed packages to a file  |
| `wmic product  <insert pipe here> find "Chef Client"`      | Show details of a package  |
| `wmic product get /format:csv <insert pipe here> find "Chef Client"`      | Show details of a package in CSV  |
| `(Get-WmiObject Win32_ComputerSystem).Name + '.' + (Get-WmiObject Win32_ComputerSystem).Domain`      |  FQDN  |
