---
layout: post
title: "Splunk ♥ CloudTrail: Detecting Dangerous Security Group Changes in Real Time"
description: "Use AWS CloudTrail with Splunk to detect dangerous EC2 security group changes in real time."
categories:
  - Security Analytics
tags:
  - monitoring
  - analytics
  - splunk
  - aws
  - cloudtrail
  - devsecops
last_modified_at: 2018-01-17T21:01:00+10:00
author: Glenn Bolton
---

The [AWS CloudTrail](https://aws.amazon.com/cloudtrail/) service records the history of AWS API calls for your account. You can use this to your advantage to automatically detect when _someone_ or _something_ makes a dangerous security group change in your account.

Also, I've already lied to you - sorry about that. The term _real time_ might be a bit of a stretch here so let me explain.

Typically, when ingesting CloudTrail logs into Splunk you'll first configure CloudTrail to send events to S3 with notifications sent to SNS + SQS when a new log file is delivered. Splunk is then configured with the CloudTrail input, which polls SQS for notifications before downloading log files from S3 which are then indexed for searching.

In practice, this typically amounts to a delay of 10-20 minutes before your CloudTrail events are searchable in Splunk. Some of this delay is on the AWS side of things and can't be avoided.

A newer approach involves using Kinesis and Splunk HTTP Event Collector to push streams of events to Splunk, rather than the standard push-pull approach. This option might work out a bit faster (I haven't tried it yet), but you'll still be limited by the speed of CloudTrail log delivery on AWS' side.

Here's how to get this setup, and how to search for security group changes that open specific ports to the internet.

## 1. Enable CloudTrail
This part's a piece of cake and can be done entirely in the AWS console:
1. [Configure CloudTrail to send events to S3](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html)
2. Make sure you've enabled [SNS notifications](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/configure-sns-notifications-for-cloudtrail.html) 
3. [Create a standard SQS queue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-create-queue.html)
4. [Subscribe the queue to the SNS topic](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-subscribe-queue-sns-topic.html) from step 2.

## 2. Configure Splunk
1. [Follow the instructions for configuring an *SQS-based* S3 input](https://docs.splunk.com/Documentation/AddOns/released/AWS/SQS-basedS3).

## 3. Search for Events
Let's get straight to business, here's the query you need to find security group changes where potentially dangerous ports are exposed to the internet:

```text
index=aws-cloudtrail eventName=AuthorizeSecurityGroupIngress
| spath output=ipp path=requestParameters.ipPermissions.items{}
| spath output=securityGroup path=requestParameters.groupId
| spath output=user path=userIdentity.arn
| mvexpand ipp
| spath input=ipp output=fromPort path=fromPort
| spath input=ipp output=toPort path=toPort
| spath input=ipp output=ipRanges path=ipRanges
| spath input=ipRanges output=srcCidrs path=items{}.cidrIp
| mvexpand srcCidrs
| search srcCidrs="0.0.0.0/0"
| search (
    (fromPort <= 22 AND toPort >= 22) OR 
    (fromPort <= 3389 AND toPort >= 3389) OR 
    (fromPort <= 3306 AND toPort >= 3306) OR 
    (fromPort <= 1433 AND toPort >= 1433)
  )
| table _time, user, securityGroup, fromPort, toPort, srcCidrs
```

In case you're new to Splunk, or not maybe just not familiar with `spath` and `mvexpand`, let me break this query down:

1. `index=aws-cloudtrail eventName=AuthorizeSecurityGroupIngress`<br>
  - This tells Splunk to search for events in the 'aws-cloudtrail' index which have the CloudTrail event name `AuthorizeSecurityGroupIngress`. If your index is called something else, you will need to adjust this line accordingly.
1. The `spath` [📚](https://docs.splunk.com/Documentation/SplunkCloud/6.6.3/SearchReference/Spath) command lets you easily perform inline field extraction from complex data types, like JSON.
 - Here we pull out some useful parts of the JSON event, like the [IpPermission](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_IpPermission.html) data, referring to it here as `ipp`.
1. You'll notice two `mvexpand` [📚](http://docs.splunk.com/Documentation/SplunkCloud/6.6.3/SearchReference/Mvexpand) commands. 
 - An `AuthorizeSecurityGroupIngress` event can contain multiple `IpPermission`s, so we expand these into separate events. 
 - An `IpPermission` can contain more than one source CIDR, so we expand these into separate events as well.
1. `| search srcCidrs="0.0.0.0/0"`
  - This tells Splunk that we're only interested in the events which open access to the internet (0.0.0.0/0 - all source IP addresses).
1. `(fromPort <= 22 AND toPort >= 22)`
 - Querying for events where the lower port is less than or equal to our offending port, and the upper port is greater than or equal to the offending port will ensure that we pick up violations when the port is part of a range. A range of 20 - 30 for example will include port 22, this approach picks these up.
 1. `| table _time, user, securityGroup, fromPort, toPort, srcCidrs`
   - This formats things nicely 🤷.

## The better way
**_The better way_ is security as code offering reliable, repeatable and scalable controls that automatically intervene when security problems occur.**

The [Defendable Design project](https://github.com/defendabledesign) attempts to build a standard, self-healing design for strong security on AWS using security as code to orchestrate AWS-native functionality, including [AWS CloudTrail](https://aws.amazon.com/cloudtrail/), [AWS Config](https://aws.amazon.com/config/) and [AWS Lambda](https://aws.amazon.com/lambda/).

While it's still early days, the project already **detects and automatically rolls back dangerous security group changes** and might be a better choice for you than automatically detecting, but manually remediating security misconfigurations. 

Alternatively, if your security team is already invested in Splunk, and you're not quite ready to let software make automatic changes to your security groups - this approach should serve you well.

## About some bugs
If you're managing hundreds of AWS accounts, and you have all of your CloudTrail events configured to go the same SNS topic (so that you don't have to configure Splunk whenever a new AWS account comes or goes) you might encounter a bug with the Splunk CloudTrail input where it gets stuck in a polling loop. Ask me how to fix this if this happens to you.