---
layout: post
title: Dynamic DynamoDB for monitoring and scaling Amazon DynamoDB
description: Dynamic DynamoDB for monitoring and scaling Amazon DynamoDB
#image: /assets/media/Lamp.jpg
Categories: [AWS]
tags: [AWS, dynamic-dynamodb, dynamodb, nosql, dijeeshpnair, devops, ]
comments: true
---

<p style="text-align:justify;">
In this article, I would like to share how to deploy dynamic-dynamodb for managing and scaling Amazon DynamoDB NoSQL Databases. Amazon Dynamic DynamoDB  is super fast, flexible, consistent and fully managed NoSQL database service from Amazon.  DynamoDB synchronously replicates data across three different facilities in an AWS region. This will ensure high availability and data durability.  
</p>
<br>

<p style="text-align:justify;">
You'll need specify read / write capacity units at the time of provisioning the instance and later you can scale up / down manually as per your application requirements. In order to avoid this manual intervention, we can deploy Dynamic DynamoDB and let it monitor and scale up / down the DynamoDB tables as per the requirements. </p> <br>

<p style="text-align:justify;">
Dynamic DynamoDB is a opensource tool created by `[Sebastian Dahlgren](https://github.com/sebdah/dynamic-dynamodb)`. You can either use the CloudFormation template provided in the [github repository](https://github.com/sebdah/dynamic-dynamodb/blob/master/cloudformation-templates/dynamic-dynamodb.json) to provision Dynamic DynamoDB instance or you can manually set one yourself by following the guidelines given bellow.
</p> <br>

----------


How it works
----------------

<p style="text-align:justify;">
Dynamic DynamoDB is a small script that will monitor DynamoDB in specified intervals and scale up / down the DynamoDB table as per the policy we configured.
</p> <br>

Architecture
------

<p style="text-align:justify;">
Here is a sample architecture diagram.

![enter image description here](https://raw.githubusercontent.com/dijeesh/dijeesh.github.io/master/assets/media/dynamic_dynamoDB.jpg)


 In this scenario, I do have a custom VPC created for Web Application with 2 Public Subnets in each availability zone for Load Balancers and bastion nodes. Below that two private subnets in each availability zone for Web Service EC2 instances,  RDS multi AZ deployment for database services and below that we do have two subnets ( 1 in each AZ) for management nodes and I have configured a dynamic-dynamodb instance inside an autoscaling group with minimum 1 node running at time.  

 - dynamic-dynamodb instance is running securely in private subnets.
 - dynamic-dynamodb's high availability is ensured by autoscaling group.

</p> <br>

Setup
----

-----
