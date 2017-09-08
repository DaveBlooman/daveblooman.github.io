---
layout: post
category: aws
tags: terraform aws
---

### Not this talk again

There are a few people doing the rounds on the conference circuit that do the same "doing Terraform right" talk.  Most of these things you learn after about 4 weeks of using Terraform anyway, but actually is lacking a lot of useful information about how best to go about writing multiple production Terraform stacks.  

### Terraform modules

We jump straight into modules, the thing that should make Terraform easy to use.  I use this structure for applications.  Write your Terraform in the same repo as your code and use remote state for anything you need to import in, VPCs, subnets etc.  Unless you only run a single environment, within your Terraform directory, you will probably have a set of modules.  These modules should only include infrastructure that is used for this application, you wont be creating VPC's here.

This split is between application infrastructure and core infrastructure, core infrastructure often being shared between many services/applications

### The Structure

This is a typical application, a notifications service in this case.

<pre>
|____dev
| |____security-groups-rules.tf
| |____main.tf
| |____outputs.tf
| |____remotes.tf
| |____variables.tf
|____modules
| |____cms
| | |____alarms.tf
| | |____api-gateway.tf
| | |____api.tf
| | |____dynamo.tf
| | |____s3.tf
| | |____outputs.tf
| | |____variables.tf
| |____notifications
| | |____alarms.tf
| | |____variables.tf
| | |____outputs.tf
|____prod
| |____security-groups-rules.tf
| |____main.tf
| |____outputs.tf
| |____remotes.tf
| |____variables.tf
</pre>

As you can see, there are some common files.  

 - remotes
 - variables
 - outputs

These are in every module and environment, with cms & notifications being the modules, dev & prod being the environments.  Remotes are used for accessing core infrastructure and other applications.  An example is the notifications service needs to talk to a web service somewhere, that needs a security group ID to be passed so an inbound rule can be added.  

Variables are used mostly for things like database passwords and region information with outputs being the mechanism that allows for each service to share its resource properties.  Variables will often set defaults too, meaning that modules don't require a large amount of variables to be handed to them.Module outputs bubble up to the environment outputs and then are accessible from other environments.

### The Module Block
<pre>
module "cms" {
  source                    = "../modules/cms"
  account_id                = "${var.account_id"
  vpc_id                    = "${data.terraform_remote_state.core_eu_west.vpc_id}"
  environment               = "${var.environment}"
  front_end_instance_id     = "${data.terraform_remote_state.core_eu_west.instance_id}"
  remote_access_cidr_blocks = "${jsonencode(var.remote_access_cidr_blocks)}"
  bucket_resource_name      = "hello-world"
  instance_security_group   = "${module.cms_instance.sg_id}"
  instance_role_id          = "${module.cms_instance.instance_role_id}"
  describe_tags_policy_arn  = "${data.terraform_remote_state.core_eu_west.describe_tags_policy_arn}"
  services_front_end_elb_id = "${data.terraform_remote_state.core_eu_west.front_end_elb_id}"
}
</pre>

All modules are included from the main.tf file in an environment, that file should only have module imports in.  Everything in an environment directory that isn't a module should be named well and be there for a reason.  

This module include use the file path source, this is because it is a private module, only public github modules can be used with Terraform right now, so consider that.  As you can see from our module, the only thing that is not interpolated is the bucket name, everything else either comes from remote state, another module or a variable.  The variables in this case allow for you to use this module in any account and any region assuming the remote state/modules allows for it.

You may be asking, is this useful, interpolate everything?  If you think about say, account ID, it isn't going to change in your environment, so why write it down a bunch of times.  With variables, enforcing there usage actually means using a standard set of defaults.  Think about ASG pause time or route 53 TTL, having a team that agrees on what the default is and then ignoring that and writing a hard coded value in anyway is an easy path to problems.  Using variables in this manner is sort of the same approach for programming languages, if you look at Go, the HTTP package contains a bunch of constants, https://golang.org/pkg/net/http/#pkg-constants.  Instead of writing the value `500`, you would write `http.StatusInternalServerError`.  

### The Count Trick?

You may see some code like, so what is this?

<pre>
resource "aws_s3_bucket_policy" "foo_bucket" {
  count  = "${var.use_policy ? 1 : 0}"
  bucket = "${aws_s3_bucket.foo_bucket.id}"
  policy = "${file("${path.module}/files/${var.environment_abbr}-policy.json")}"
}
</pre>

Lets say that you have some setup that requires an S3 policy to be attached only when in prod, something that prevents developers from doing something that shouldn't.  In this instance the count function is a special function that if it evaluates to 0, will not create the resource.  In this case, a variable of `use_policy` that is `false` will not provision this infrastructure.  This is actually wrapped in a ternary, so looks like some Ruby in the middle of my Terraform, but is actually a really neat way of conditionally adding infrastructure.  

I call it a trick because it goes against what i mentioned earlier about infra in modules being the same.
