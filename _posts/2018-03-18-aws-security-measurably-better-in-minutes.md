---
layout: post
title: "AWS Security: Measurably better in minutes"
description: "Use DD-AWS to better comply with the CIS AWS Foundations Benchmark."
categories:
  - Defendable Design Project
tags:
  - cis-benchmark
  - aws
  - devsecops
last_modified_at: 2018-03-18T16:58:00+10:00
author: Glenn Bolton
---

If you're not already aware of the [Center for Internet Security](https://www.cisecurity.org/), they're a non-profit organisation who do amazing work to help infosec practitioners and sysadmins [harden](https://en.wikipedia.org/wiki/Hardening_\(computing\)) their systems. 

CIS produce a series of Benchmarks (among other things) for a wide variety of technology tools and platforms. The CIS Benchmarks provide best-practice guidance on how to secure these systems.

Helpfully, the [CIS AWS Foundations Benchmark](https://d0.awsstatic.com/whitepapers/compliance/AWS_CIS_Foundations_Benchmark.pdf) tells you what you should do to help secure your AWS account.

## Measuring better
You can quickly test how your own AWS account ranks against the benchmark using the [aws-cis-foundation-benchmark-checklist](https://github.com/awslabs/aws-security-benchmark/tree/master/aws_cis_foundation_framework) from AWS Labs.

Given that the latest updates to [DD-AWS](https://github.com/DefendableDesign/DD-AWS/releases) include better coverage against the benchmark, I thought I'd do a quick before and after comparison.

For the sake of keeping it simple, I've excluded any of the _manual_ checks from my scoring, and only count the controls that the benchmark defines as _scored_.

| **Before DD-AWS** | **After DD-AWS** |
| :---------------: | :--------------: |
|<span style="font-size:300%; font-weight:bold;">27% 😞📉</span> | <span style="font-size:300%; font-weight:bold;">82% 😊📈</span>|

Obviously, your mileage may vary, and depending on how you run things the numbers in your own account might differ.

![Infographic describing the download, deploy and measure approach.](/assets/images/posts/2018-03-18-better-in-5.png)

## Download, deploy, measure
**Download** the latest release from GitHub to try it out:<br/>
[https://github.com/DefendableDesign/DD-AWS/releases/](https://github.com/DefendableDesign/DD-AWS/releases/)

**Deploy** it using the instructions here:<br/>
[https://github.com/DefendableDesign/DD-AWS/blob/master/README.md](https://github.com/DefendableDesign/DD-AWS/blob/master/README.md)

Be mindful that if your account is in a bit of a sorry state, it might be better to leave `enable_auto_response` off (it's off by default) until you're sure that you're not relying on any bad security groups or public S3 buckets.

When it comes to cost control 👛, DD-AWS costs me around $10 (USD) per month to run.
Depending on your existing free tier consumption, and the volume of CloudTrail and Config events, you might pay more than this.

**Measure** your compliance with the benchmark, before and after using AWS' checklist tool:
[https://github.com/awslabs/aws-security-benchmark/tree/master/aws_cis_foundation_framework](https://github.com/awslabs/aws-security-benchmark/tree/master/aws_cis_foundation_framework)
