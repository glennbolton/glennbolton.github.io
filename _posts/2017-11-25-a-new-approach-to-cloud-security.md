---
layout: post
title: "A new approach to cloud security"
categories:
  - Defendable Design Project
tags:
  - self-healing
  - defendable design
  - aws
  - terraform
  - secdevops
  - devsecops
last_modified_at: 2017-11-25T16:27:00+10:00
author: Glenn Bolton
---

There are [great resources](https://aws.amazon.com/whitepapers/aws-security-best-practices/) readily available to help you get to grips with best practice for AWS cloud security. Unfortunately, there's not always the time, motivation or understanding to do things _the right way_. Deadlines loom, documentation is skipped and misunderstandings happen.

The basics of good cloud account security practice are going to look pretty much the same, regardless of the types of workloads running within the account or the preferred ways of working for your devops teams.

Instead of relying on _people_ to make expert security choices, to select the right setting, to check the right box and to fully understand the security implications of every action that they take, maybe there's a better way. Maybe there's a way that's cheap, reliable, repeatable and scalable. 


## The better way
The words _reliable_, _repeatable_ and _scalable_ are now fairly common to hear from any mature devops team or in any description of any modern cloud architecture. 

**The principles of infrastructure as code, auto-scaling and self-healing are now mainstream.**

Unfortunately, the language of information security is still often very much about policies and procedures, and about _the right way_ vs _the wrong way_. There are obviously exceptions to this within the security teams of great cloud-first companies and with the first-movers who are still trying to define what DevSecOps/SecDevOps means.

What would it look like if we took solid security controls and managed them in a way that was reliable, repeatable and scalable? You'd have _security as code_.

**_The better way_ is security as code offering reliable, repeatable and scalable controls that automatically intervene _when security problems occur_.**


## The Defendable Design Project
The [Defendable Design project](https://github.com/defendabledesign) is my attempt at building a standard, self-healing design for strong security on AWS using security as code to orchestrate AWS-native functionality, including [AWS CloudTrail](https://aws.amazon.com/cloudtrail/), [AWS Config](https://aws.amazon.com/config/) and [AWS Lambda](https://aws.amazon.com/lambda/).

Using [Terraform](https://www.terraform.io/), [DD-AWS](https://github.com/DefendableDesign/DD-AWS/releases) enables AWS Config and deploys a series of Config Rules that check for common problems. The current release also includes the ability to automatically reverse dangerous security group changes.

The nearterm goal is to align the project with an industry-standard security guideline (probably the Center for Internet Security's [CIS AWS Foundations Benchmark](https://www.cisecurity.org/benchmark/amazon_web_services/)) and to continue building out automatic remediation capabilities.


## TL;DR
I built a thing:
* It's written in Terraform, so it's idempotent
* It checks for security in your AWS account
* It responds to security misconfigurations in realtime(ish) and **automatically fixes those mistakes**
* It's early days, so there's a lot more to do
* You can find the [latest release on github](https://github.com/DefendableDesign/DD-AWS/releases).