---
layout: post
title: "How To: Self-healing Security on AWS"
description: "How Defendable Design enables self-healing security on AWS."
categories:
  - Defendable Design Project
tags:
  - self-healing
  - continuous compliance
  - continuous enforcement
  - design
  - architecture
  - diagram
  - security
  - aws
  - devsecops
last_modified_at: 2018-03-21T22:25:00+10:00
author: Glenn Bolton
---
<style type="text/css">@import url(&"https://fonts.googleapis.com/css?family=Open+Sans:400,700");</style>

Defendable Design for AWS can automatically find and selectively fix both dangerous security group rules and leaky S3 buckets 🙌. It even keeps you in the loop about what's happening in real time on Slack.

This post is an overview of the AWS infrastructure and services that enable this — serverlessly.

## Architecture
![Architecture diagram for DD-AWS.](/assets/images/posts/2018-03-21-dd-aws-config.svg)

[View this diagram on Cloudcraft](https://cloudcraft.co/view/ad1df36f-fe5a-4976-ae1e-41dd72ee3777?key=mw7dG2RrzEFIrikeNqOudQ)

## How it works
### Config Notifications
These steps are represented by the top and left parts of the diagram.
1. **AWS Config**:  
Config records configuration changes in the account. 
1. **Config Notifications**:  
Changes detected by AWS config are sent as notifications to SNS which sends these notifications to the Notifier Lambda function. 
1. **Notifier**:  
The Notifier function _selectively_ sends some of these notifications - just the ones about config rule status changes - to Slack.

### Config Rules
These steps are shown in the centre-left of the diagram.
1. **Config Rules**:  
A combination of AWS-managed and custom Config Rules continuously measure the compliance of assets in the account to look for problems.  
The status changes for these rules is also recorded by Config.  
1. **Custom Config Rule Functions**:  
The custom config rules are backed by Python-based Lambda functions. These functions perform compliance checks which return results back to AWS Config. When a compliance failure is detected the function pushes a message with the required steps to fix the failure to the central Remediation Queue.
1. **Remediation Queue**:  
This is where remediation actions queue, waiting to be picked up.

### Remediation
These steps are shown in the top-right and centre-right.
1. **Scheduled Event Rule**:  
A CloudWatch scheduled event fires every 60 seconds to launch the Remediation Coordinator function.  
When you set `enable_auto_response` to false, it disables this scheduled event. The remediation tools are still built by Terraform, they're just never triggered.
1. **Remediation Coordinator**:  <br>The Remediation Coordinator function performs two main functions.
    1. It picks up _things to fix_ from the Remediation Queue.
    1. It determines which Remediation Function is the right one for that particular type of problem and forwards a copy of the message to it.
1. **Remediation Function**:  
The relevant Remediation Function performs the required Remediation Action to fix that problem, and then deletes the original source message from the Remediation Queue. It then sends a message to the Notifier function about the actions it took.
1. **Notifier**:  
The Notifier function formats and forwards this message to Slack.

## Continuous Compliance with Continuous Enforcement
This design provides a model for enabling _continuous compliance_ and _continuous enforcement_ in your AWS accounts, whether you download what I've already written, or take the DIY path.

If you want to give this a try:
- Download the [latest release](https://github.com/DefendableDesign/DD-AWS/releases/) from GitHub.
- Deploy it using the instructions in the [README](https://github.com/DefendableDesign/DD-AWS/blob/master/README.md).

If you're already running production workloads in your account, definitely leave `enable_auto_response` off (it's off by default) until you're sure that you're not relying on any bad security groups or public S3 buckets to keep things running. 

You can check what automated response actions would have been taken by taking a peek at the messages in the Remediation Queue.
