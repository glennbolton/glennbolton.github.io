---
layout: post
title: "What's the deal with encryption at rest?"
description: "Encryption at rest lets you revoke access to data when you don't control the full storage lifecycle."
categories:
  - Cloud Security
tags:
  - aws
  - azure
  - encryption
  - encryption at rest
last_modified_at: 2018-04-21T18:59:00+10:00
author: Glenn Bolton
---

_Encryption at rest_ means that your data is encrypted as sits on disk, it's the flipside to _encryption in transit_, which is the encryption of your data as it passes between systems. SSL/TLS is a good example of encryption in transit.

If you've spent much time working with the big cloud providers like AWS or Azure, you might have noticed that they make it pretty easy to encrypt your data at rest. You might have also struggled to think of a good reason for doing this, after all, no one is going to ram-raid an AWS data centre to steal a SAN with your data on it.

## I can just _delete_ my data when I don't need it anymore
Sure, you can delete your block storage volumes and object storage repositories through your cloud provider's console or API.

Unfortunately, this _deletion_ process doesn't guarantee the _destruction_ of your data.

## So what's the difference between _deletion_ and _destruction_?

_Destruction_ of your data means that it's gone through a process that makes it impossible (or exceptionally hard) to recover.

When you send a delete request to your cloud provider, your data might disappear from your view immediately, but it also might:
 - Stick around on a backend while waiting for a clean-up process
 - Persist on the underlying storage
 - Exist in backups.
 
 All of these things are outside of your knowledge and control.


<span style="font-size: 150%">_Deletion_ is putting your sensitive documents in the bin.<br>_Destruction_ is shredding or burning them.</span>  
<span style="font-size: 300%">It's 🗑️ vs 💥🔥.</span>

## Why should I care about _destroying_ my data?

In most jurisdictions, if you collect personal information about your customers or users, you have specific legal obligations around protecting that data. You can't outsource responsibility for the protection of that data - you need to make sure that you can track and manage the lifecycle of that data, including its destruction when it is no longer needed.

## If I can't just _delete_ it, how do I _destroy_ it?

In a traditional on-premise environment you might have destroyed your data by using a secure disk wipe process and then verifying the physical destruction of the storage media. Cloud providers do the same thing, but you don’t have any control over this process or any real assurance that they destroyed your specific data adequately.

<span style="font-size: 150%">Encryption at rest helps you destroy your data.</span>  
Let that sink in for a minute, and I'll explain.

You can destroy your data by destroying the keys that unlock (decrypt) that data.

By destroying your keys, you destroy the ability to decrypt your data. If the data can't be decrypted, it can't be used. This process is called [crypto-shredding](https://en.wikipedia.org/wiki/Crypto-shredding).

<span style="font-size: 300%">🔑+💥=😃👍💯</span>

While cloud providers don't give you many options to destroy your data on disk, their key management systems are the exception to this rule. Deletion of an AWS KMS Customer Master Key (CMK) for example is [_destructive_ and _irreversible_](https://docs.aws.amazon.com/kms/latest/developerguide/deleting-keys.html) (their words, not mine).

Encryption at rest helps you control the lifecycle of your data, even when you don’t control the storage.

<span style="font-size: 150%">Enable encryption **before** you up upload your data.</span>

On AWS use [S3 encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html) and [EBS encryption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html), and on Azure, the equivalent [Storage Service Encryption](https://docs.microsoft.com/en-us/azure/storage/common/storage-service-encryption)  and [Azure Disk Encryption](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption).

