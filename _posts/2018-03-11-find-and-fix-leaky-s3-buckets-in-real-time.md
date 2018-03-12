---
layout: post
title: "Find and Fix Leaky S3 Buckets in Real Time 🔧⏳👏"
description: "Use self healing security on AWS to automatically find, fix and prevent potential S3 data breaches."
categories:
  - Defendable Design Project
tags:
  - monitoring
  - aws
  - cloudtrail
  - self-healing
  - defendable design
  - s3
  - terraform
  - devsecops
last_modified_at: 2018-03-11T13:16:00+10:00
author: Glenn Bolton
---

An alarmingly large number of severe data breaches lately have been attributed to poorly configured S3 buckets. Unfortunately, we see this [time and time and time again](https://www.google.com.au/search?q=s3+bucket+site%3Atheregister.co.uk).

The good news is that latest update to [DD-AWS](https://github.com/DefendableDesign/DD-AWS/releases) now includes self healing for leaky S3 buckets 🎉.

## How it works

1. A custom [AWS Config Rule](https://aws.amazon.com/config/) checks S3 buckets for dangerous permissions in real time (almost) whenever a change occurs.

1. When a bucket is detected as non-compliant, the Config Rule status changes, and a remediation task is pushed to an [SQS queue](https://aws.amazon.com/sqs/).

1. The `Remediation_Coordinator` [Lambda function](https://aws.amazon.com/lambda/) periodically (every 1m) pulls tasks from the Remediation queue and fires off the correct function to fix the problem, in this case the `S3_PublicAccess_Remediation` function.

1. The `S3_PublicAccess_Remediation` function will _selectively_ remove the dangerous part of the Access Control Policy, or Bucket Policy and then call the AWS API to `PutBucketAcl` or `PutBucketPolicy` to remediate the risk.

1. If you've enabled Slack integration, the `Notifier` function lets you know everything that's happening, at every step of the way:


## What it looks like in action
![Image of Slack alerts for a leaky S3 Bucket](/assets/images/posts/2018-03-11-slack-integration.png)


## What it costs
DD-AWS is open source and is released at no cost under a MIT license.

The current version costs around USD $10 per month to run from underlying AWS infrastructure costs. This price will depend on the volume of your CloudTrail logs for S3 and CloudWatch Logs storage costs.


## Get it from GitHub
Download the latest release from GitHub to try it out:
[https://github.com/DefendableDesign/DD-AWS](https://github.com/DefendableDesign/DD-AWS)