---
layout: post
title: Dynamic DynamoDB for monitoring and scaling Amazon DynamoDB
description: Dynamic DynamoDB for monitoring and scaling Amazon DynamoDB
#image: /assets/media/Lamp.jpg
Categories: [AWS]
tags: [AWS, dynamic-dynamodb, dynamodb, nosql, dijeeshpnair, devops, ]
comments: true
---

<p style="text-align: center;">
In this article, I would like to share how to deploy dynamic-dynamodb for managing and scaling Amazon DynamoDB NoSQL Databases.
</p>

<p style="text-align: center;">
Amazon Dynamic DynamoDB  is super fast, flexible, consistent and fully managed NoSQL database service from Amazon.  DynamoDB synchronously replicates data across three different facilities in an AWS region. This will ensure high availability and data durability.  
</p>

<p style="text-align: center;">
You'll need specify read / write capacity units at the time of provisioning the instance and later you can scale up / down manually as per your application requirements. In order to avoid this manual intervention, we can deploy Dynamic DynamoDB and let it monitor and scale up / down the DynamoDB tables as per the requirements. </p>

<p style="text-align: center;">
Dynamic DynamoDB is a opensource tool created by `[Sebastian Dahlgren](https://github.com/sebdah/dynamic-dynamodb)`
</p>

<p style="text-align: center;">
You can either use the CloudFormation template provided in the [github repository](https://github.com/sebdah/dynamic-dynamodb/blob/master/cloudformation-templates/dynamic-dynamodb.json) to provision Dynamic DynamoDB instance or you can manually set one yourself by following the guidelines given bellow.
</p>

----------


How it works
----------------

<p style="text-align: center;">
Dynamic DynamoDB is a small script that will monitor DynamoDB in specified intervals and scale up / down the DynamoDB table as per the policy we configured.
</p>
Architecture
------

<p style="text-align: center;">
Here is a sample architecture diagram. In this scenario, we do have a custom VPC created for Web Application. Load Balancers are configured in Public Subnets, Web Service Layer is configured in dedicated private subnets, RDS multi AZ deployment for database services and bellow that we do have two subnets ( 1 in each AZ) for management nodes and I have configured a dynamic-dynamodb instance inside an autoscaling group with minimum 1 node running at time.
</p>


-----
