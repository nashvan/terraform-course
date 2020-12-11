# terraform-course

* These files are part of my Udemy course about Terraform
* Course URL: https://www.udemy.com/learn-devops-infrastructure-automation-with-terraform/?couponCode=TERRAFORM_GIT

# Compatibility

* This is the >=terraform-0.12 branch. For compatibility with older versions, use the terraform-0.11 branch.

# Demo overview
Demo Directory | Description
------------ | -------------
first-steps | First steps
demo-1 | First steps: Launching an EC2 instance
demo-2 | Using provisioner
demo-2b | Using provisioner on a Windows instance
demo-3 | Executing script locally
demo-4 | Outputting
demo-5 | Data Source
demo-6 | Modules
demo-7 | AWS VPC
demo-8 | EC2 instance within VPC with securitygroup
demo-9 | EC2 instance with EBS volumes
demo-10 | Userdata and cloudinit
demo-11 | Route53 (DNS)
demo-12 | RDS
demo-13 | IAM
demo-14 | IAM Roles with S3 bucket
demo-15 | Autoscaling
demo-16 | Autoscaling with ELB (Elastic Load Balancer)
demo-17 | Elastic Beanstalk PHP 7 stack with RDS
demo-18 | Interpolations, VPC module
demo-18b | Project structure, best practices
packer-demo | Build AMIs with Packer
jenkins-packer-demo | Demo with jenkins and Packer
docker-demo-1 | Using ECR - The EC2 Container Registry
docker-demo-2 | Using ECS - The EC2 Container Service
docker-demo-3 | Using ECR/ECS with Jenkins in a complete workflow
module-demo | Using ECS + ALB in 4 modules to show how developing terraform modules work


##
## Terraform functions with examples:

resource "aws_ecs_cluster" "this" {
  count = var.create_ecs ? 1 : 0

  name = var.name

  setting {
    name  = "containerInsights"
    value = var.container_insights ? "enabled" : "disabled"
  }

  tags = var.tags
}

==========================

## Lookup:
lookup(map, key, default)
Examples
> lookup({a="ay", b="bee"}, "a", "what?")  
ay 
In the example above the result was (ay) because the key was to search for value of (a) in the list


> lookup({a="ay", b="bee"}, "c", "what?")
what?
In the example above, the result was (what?) because the key was to search for the value of c in the list. And there is no © in the list, so it will take the default value.

https://www.terraform.io/docs/configuration/functions/lookup.html
======================

## Length:
length determines the length of a given list, map, or string.
> length([])
0
> length(["a", "b"])
2
> length({"a" = "b"})
1
> length("hello")
5
===============================================

## element:
element retrieves a single element from a list.

> element(["a", "b", "c"], 1)
b

> element(["a", "b", "c"], 3)
a
It counts it like this:
(0,1,2,
3,4,5
6,7,8
=================================================

## For each
https://www.terraform.io/docs/configuration/meta-arguments/for_each.html



=================================================

## For
https://www.terraform.io/docs/configuration/expressions/for.html



=================================================

## Dynamic:
https://www.terraform.io/docs/configuration/expressions/dynamic-blocks.html


 dynamic "setting" {
    for_each = var.xyz
    content {










```
locals {
  # convert from list to map with unique keys
  recordsets = { for rs in var.records : "${rs.name} ${rs.type}" => rs }
}

resource "aws_route53_record" "this" {
  for_each = var.create && (var.zone_id != null || var.zone_name != null) ? local.recordsets : tomap({})

  zone_id = data.aws_route53_zone.this[0].zone_id

  name    = each.value.name != "" ? "${each.value.name}.${data.aws_route53_zone.this[0].name}" : data.aws_route53_zone.this[0].name
  type    = each.value.type
  ttl     = lookup(each.value, "ttl", null)
  records = lookup(each.value, "records", null)
  

  dynamic "alias" {
    for_each = length(keys(lookup(each.value, "alias", {}))) == 0 ? [] : [true]

    content {
      name                   = each.value.alias.name
      zone_id                = each.value.alias.zone_id
      evaluate_target_health = lookup(each.value.alias, "evaluate_target_health", false)
    }
  }

  allow_overwrite = true
}

```


