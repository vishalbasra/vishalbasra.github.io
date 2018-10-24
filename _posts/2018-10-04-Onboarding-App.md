---
layout: post
title:  "On boarding a new responsibility at work"
categories: Rambling

categories:
  - Rambling

tags:
  - Rambling

excerpt: On boarding a new application or service at work
---

**Read data from a file on disk to process**

1. Introduction:
- What is the application about? - brief introduction - 5/10 mins
- What OS does this run on ?
- Platform details - versions / ports / services


2. Where is it used?

- What apps / services use it ?
- What if it goes down / unavailable in an environment / region - what's the impact?


3. Design

- What's the design / architecture like for a single environment?
- Does this conform with industry standards or is this specific to our company?
- What are the different moving parts?
     Nodes
     Load balancers / F5
     configs
     App configs
     CFN template
-   What are it's different components - web,backend,db,workers,queues etc. ?

4. Environments

- What are the different environments?
- Are the environments themselves different in any way, i.e do they have nuances?


5. Expectations / role

- What are the expectations / role we usually perform?
     Admin
     Patching
     Maintaining
     Upgrade
     Anything else?


6. Management

- How do we currently fulfill our expectations?
- Source control / configuration management?


7. Major Incidents

- What have been some of the major incidents that have occurred in the past pertinent to this application?

8. Communication

- Internal - escalations / channels / help groups?
- External - if this is licensed : support / community / forums / ICR / community slack (perhaps)?


9. Further reading / links

- Any links and/or documentation should go here
