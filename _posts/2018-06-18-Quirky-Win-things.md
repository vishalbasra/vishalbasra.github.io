---
layout: post
title:  "Quirky Win things"

categories:
  - windows

tags:
  - Windows
  - cmd


excerpt: Some quirky albeit nice windows things!
excerpt_separator:  <!--more-->

permalink: /:categories/:title/
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
| `.ToLower() or .ToUpper()`      |  lower or UPPER case  |
| `Get-Service "vishal*"`      |  Find and list status of all services containing the specified name as the ServiceName  |
| `Start-Service -Name Vishal`      |  Starts the service where name matches the specified value use `-DisplayName` instead if you want  |
