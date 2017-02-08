---
layout: post
category: aws
tags: aws microservice codedeploy
---

We have a familiar story, big monolith, trying to break into microservices, same old same old.  We have a dotnet stack, on Windows, which is deployed using Octopus and TeamCity CI.  We wanted to use Go, but weren't interested in the Docker route, so what do you choose in that situation?

### The change

When deciding to move away from dotnet for new applications, there are some questions around culture, workflow and learning.  If you are building and testing code on a CI server, then deploying via CI to your environments, it makes to keep the same approach, but change everything about it.

We did some direct replacements, Linux for Windows, Golang for C# and Codedeploy for Octopus.  This may seem like a odd choice, but it actually allowed to achieve our core goal, How quickly can get into production and deploy whenever we want.  Considering there was no existing system for doing this on Linux within the company, we had a lot of opportunities, but our decisions were based on experience with the technology and how effectively we thought we could run it in production.

### CodeDeploy

For those unfamiliar with CodeDeploy, its a system that allows for deploying anything to a bunch of servers using simple scripts and uses a straight forward YAML file to describe the deploy.  

The key components are applications, deployment groups, appspec files and revisions.  Deployment groups are the servers you are going to deploy to, based on AWS EC2 tags or auto scaling group name.  An appspec file is the YAML file you will use to describe your deployment, before and after the code is updated, what files to cleanup, where to put new files etc.  Revisions are typically zip or tar files in S3 containing your code ready to be unpacked onto the servers.

### Why CodeDeploy

It sounds like a straight forward service, and it is.  For a startup like FundApps with 12 on the engineering team, having a tool that is hosted, has a solid API and is affordable, because its free on AWS.  My preferred way of deploying servers is the bakery model, packaging up AWS images, AMI's, then rolling them out in autoscaling groups.  This is nice because you can use golden images, something that you can roll back to if the new image is not working for example.  This can be quite slow though, especially for a 10MB Go binary, it is actually quite a costly process.

CodeDeploy takes only a few seconds to deploy and logs all the information about your deployment as it happens, if something goes wrong, it can roll back.  There are options for how you would like your deployment to go as well, all servers at once, one at a time or custom roll outs.  If a one at a time deployment failed on server 1, it will not roll out to server 2 and 3 for example, which gives an opportunity to investigate the deployment.

The UI for CodeDeploy is quite clumsy, there are many clicks to get to the information you want and it is hard to jump to the logs quickly.  We use Teamcity for all our deploys though, which makes continuous delivery with CodeDeploy simple to setup.  We will only use the UI when we have to debug something that is prevent a deploy.

### envconsul
envconsul can be used to drive dynamic config changes from the Consul KV store and restart your application to take the values.  If you are currently having to rebuild your server or Docker image because you bake in config, you probably know how a single character mistake can cause you to rebuild and redeploy.  envconsul is the best solution I have seen to prevent that, by giving you lots of options in how the app restart, what values from Consul are going to be injected into your app and what the behaviour should be if something fails. 
