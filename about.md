---
layout: page
title: About
---

## Employment

**FundApps**  
Infrastructure Engineer
2016 - Present

**BBC News**  
Senior Developer
2011 - 2016

### Github
[https://github.com/DaveBlooman](https://github.com/DaveBlooman)

### Skills / Technical Knowledge

**Languages** : Ruby, Go  
**Cloud** : AWS  
**Provisioning**: Docker, Vagrant, Packer  
**Monitoring** : InfluxDB, Graphite, Grafana, Cloudwatch  
**Load Testing** : Jmeter, Gatling  
**Testing** : RSpec/Cucumber, Wraith  
**CI** : Jenkins, Travis  
**Version Control** : Git  
**Other** : Sinatra

### Personal Projects

**EVA** - A Docker web control interface  - [Github Link](https://github.com/DaveBlooman/eva)  
**Linkey** - Link checker and simple integration test tool  - [Github Link](https://github.com/DaveBlooman/linkey)  
**Gatling Docker** - Simple way of viewing data from Gatling using InfluxDB  - [Github Link](https://github.com/DaveBlooman/gatling-docker)  
**Mass Site Testing** - Solution to testing 30+ sites from 1 codebase  - [Github Link](https://github.com/DaveBlooman/page-objects-example)  

### Projects

**[Acervos](https://github.com/acervos)** - Repo containing AWS cloudformation and code to help provision services easily.

**[Sonitus](https://github.com/BBC-News/sonitus)** - Sontius is a project that works with AWS Lambda to provide Cloudwatch Alarms into a Slack channel.  This is written in Go as it was something I wanted to learn, but also allowed for a single binary to be used.  More information about Sontius can be found in my [blog post here](http://www.dblooman.com/aws/2015/05/02/creating-an-alarm-service-using-AWS-lambda-and-slack/).

**[Wraith](https://github.com/BBC-News/wraith)** - Wraith is an open source tool for visual regression testing.  It was created as a solution to the CSS regressions that were being introduced into the codebase.  It is used by developers and testers to compare a given domain, such as a live site, and a local dev environment.  This produces a gallery showing the differences between the two images, as well as indicating the percentage difference.
Wraith is written in Ruby with PhantomJS and ImageMagick used to save and compare the images.

**[Linkey](https://github.com/DaveBlooman/linkey)** - Linkey is a simple tool for checking websites for broken links.  I wrote the tool as I found that pages that were rolling out a new navigation were linking off to invalid URLs.  Linkey can check a page for all links, regex to a point in the URL, then check they are vaild against either the same base URL or a different environment.  It has a second function in that it does a simple status check return an error for anything other than a 200, this combined with a CI job makes testing the status of Integration environments for broken pages/links very quick.

**[Responsive News](http://www.bbc.co.uk/news)** - Created and implemented several test strategies as the team and codebase grew.  This started off with simple tests, use of Cucumber and manual testing.  In order to reduce CSS testing, I created the Wraith tool.  In a first attempt to stop using Cucumber, I created a set of acceptance tests that would work cross browser and work with BrowserStack, this proved to be too fragile and didn’t detect some bugs within the 32 World service sites that use the codebase.  The second solution was to create a simplified version of these tests, allowing for configuration to be added and tests to be turned on or off based on site.  These tests allowed us to greatly reduce the time spent checking multiple sites as the tests were reusable.  We can also specify environment and width for responsive testing.

**AWS Jmeter/Gatling** - In wanting to test our AWS components, I needed an effective way to load test on demand, I accomplished this using JMeter, a Java based load testing tool and AWS.  The workflow included creating a load test script, committing to Github, having our EC2 instance build, provision our cloudformation and wait for completion.  Once complete, a CI job would be used to a message to SQS containing the name of the load test script.  This message would activated a cloud watch alarm, bringing up the EC2 instance and starting a JRuby application.  The JRuby app consumed the message and fed it to the command line where  well, with many commenting on the detail and accuracy of the posts.  
After several months of using Jmeter, we moved to [Gatling](www.gatling.io), a Scala based load testing tool.  I updated our load test scripts and impleted Gatling into the project, learning some Scala along the way.

**Monitoring** - With most of our infrastructure on AWS, we needed to ensure that we had adequete monitoring of application metrics as well as instance metrics.  I investigated many solutions such as Graphite and InfluxDB, of which we settled on InfluxDB.  I worked to bring the concepts we prototyped in Docker containers to BBC's deployment tool, Cosmos, writing Cloudformation and scripts to make the application work.  

**Javelin** - Initially created by BBC Sport, I took the lead, maintained and contributed improvements to the project for News.  Javelin allows for developers to take a Git branch from a project and pull it into a Docker container for testing.  This allows for developers, BA's, testers and stake holders to view code in development for immediate feedback.  The application is based on Sinatra/Ruby, which provisions Docker containers on AWS.  The container is based on a developer sandbox running CentOS 5 and comes pre-baked with all dependencies for running the branch.  At creation time, the branch also has other tasks run as what would normally be a run script.  This would include, CSS compilation, restarting Apache, starting Memcache, pulling in related repos with Composer and checking for options passed by the developer to the interface, such as container name.  This is as close to having multiple test environments as you can get get, with lots of News specific code added by me.
