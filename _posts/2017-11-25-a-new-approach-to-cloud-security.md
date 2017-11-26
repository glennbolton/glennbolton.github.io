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

[Quality resources](https://aws.amazon.com/whitepapers/aws-security-best-practices/) that help you get to grips with best practice for AWS cloud security are easy to find. Unfortunately, there’s not always the time, motivation or understanding to do things _the right way_. Deadlines loom, critical documentation is skipped over, and sometimes, people just get things wrong.

The fundamentals of effective cloud account security look pretty much the same, regardless of the types of workloads running within the account or the technology preferences of your DevOps teams. **There are _known good_ and _known bad_ practices, most of which we can identify programmatically.**

Instead of relying on _people_ to make expert security choices, to select the right setting or check the right box, and to fully understand the security implications of every action that they take, maybe there’s a better way. Maybe there’s a way that’s cheap, reliable, repeatable and scalable.

## The better way
The words _reliable_, _repeatable_ and _scalable_ are relatively common to hear from any experienced DevOps team or in any description of a modern cloud architecture. 

**The principles of infrastructure as code, auto-scaling and self-healing are now mainstream.**

Unfortunately, the language of information security is still often very much about policies and procedures, about dogmatism over pragmatism, about saying no. There are exceptions to this in the security teams of some forward-thinking companies and with the first-movers who are still trying to define what DevSecOps/SecDevOps means.

Imagine what it might look like if we took solid security controls and managed them in a way that was reliable, repeatable, scalable and self-healing. 

**_The better way_ is security as code offering reliable, repeatable and scalable controls that automatically intervene when security problems occur.**


## The Defendable Design project
The [Defendable Design project](https://github.com/defendabledesign) is my attempt at building a standard, self-healing design for strong security on AWS using security as code to orchestrate AWS-native functionality, including [AWS CloudTrail](https://aws.amazon.com/cloudtrail/), [AWS Config](https://aws.amazon.com/config/) and [AWS Lambda](https://aws.amazon.com/lambda/).

**Using [Terraform](https://www.terraform.io/), [DD-AWS](https://github.com/DefendableDesign/DD-AWS/releases) enables AWS Config and deploys a series of Config Rules that check for common problems. Best of all, the current release can automatically reverse dangerous security group changes.**

The near-term goal is to align the project with an industry-standard security guideline (probably the Center for Internet Security's [CIS AWS Foundations Benchmark](https://www.cisecurity.org/benchmark/amazon_web_services/)) and to continue building out automatic remediation capabilities.

The long-term goal is to provide a _one size fits most_ package of code that's safe and easy to deploy, which hardens your environment and automatically responds to misconfigurations in real-time, enabling _continuous compliance_.


## TL;DR
I built a thing:
* It's written in Terraform, so it's idempotent
* It checks for security in your AWS account
* It responds to security misconfigurations in near real-time and **automatically fixes those mistakes**
* It's early days, so there's a lot more to do
* You can find the [latest release on GitHub](https://github.com/DefendableDesign/DD-AWS/releases).