---
layout: post
title:  "Docker Things"

categories:
  - containers

tags:
  - Docker
  - containers
  - cmd
  - metadata

excerpt: Handy docker stuff
excerpt_separator:  <!--more-->

permalink: /:categories/:title/
---

| Quirky thing        | Explanation  |
| ------------- |:-------------:|
| `docker ps -a`      | List all containers  |
| `docker exec -it <contid> bash`      | Connect to a container with bash as the shell  |
| `Connect to ECS container`      | Cluster -> Service -> Task -> Ec2 id -> login to Ec2 -> login to the container  |
| `registry garbage-collect /etc/docker/registry/config.yml --dry-run > garbage.log`      | On a private registry, run to see all blobs which can be deleted  |
| `curl -X GET http://<pvt-dckr-registry>/v2/_catalog | jq`      | Get all the list of repos/images on a private registry, stdout this to a file, jq/prettyprint  |
| `for i in $(cat useless.json) ; do curl -X GET http://<pvt-dckr-registry>/v2/$i/tags/list ; done`      | Loop over all images to get all tags, stdout this to a file, jq/prettyprint and save  |
