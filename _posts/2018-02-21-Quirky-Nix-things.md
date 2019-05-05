---
layout: post
title:  "Quirky Nix things"

categories:
  - Linux

tags:
  - Linux
  - bash


excerpt: Some quirky albeit nice nix things!
excerpt_separator:  <!--more-->

permalink: /:categories/:title/
---
| Quirky thing        | Explanation  |
| ------------- |:-------------:|
| `curl -v telnet://<name>:<port>`      | `telnet` via `curl`  |
| `curl -x 'http://10.10.10.1:3128' -v -X POST 'http://10.10.10.10/api/find-flavour' -H 'flavour:cola'`      | `curl` via a `proxy`  |
