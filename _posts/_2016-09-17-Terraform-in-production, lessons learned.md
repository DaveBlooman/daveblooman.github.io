---
layout: post
category: aws
tags: codedeploy aws
---

Infrastructure as code is something you will probably here when associated with DevOps.  The idea is simple, let developers worry about infrastructure, let Ops worry about the little things like CDNs, security, caching, DDOS prevention etc.  

With more tooling being based on code, rather than push buttons and hand written scripts, there has been a transition up and down the industry. Front end developers don't do HTML and CSS, they write HTML in JavaScript and write JavaScript tooling to convert everything into something else.  The same thing has happened for developers, with the term, "full stack developer" becoming a thing.

AWS, GCE, Azure, they all have large amounts of tooling for automating management of your infrastructure, both UI and command line driver.  It is the idea that you can wrote code to driver your infrastructure that has become an essential part of daily like for developers and those who practice DevOps, which one of the main contenders being Terraform.

### Intro
Terraform is easy to grasp for anyone who has worked with languages like JSON and Yaml, blocks of components, often with nested components inside them with labels for descriptions.  Terraform, is an API abstraction over multiple cloud providers.  There are other tools, like Ansible, that do this, but Terraform has a unique approach, it uses Hashicorp Configuration language, HCL.  This means that there is consistency between all the providers, how resources can depend on each other and much training will be required to learn.  
