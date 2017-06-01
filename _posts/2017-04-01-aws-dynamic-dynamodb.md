---
layout: post
title: Dynamic DynamoDB for Autoscaling Amazon DynamoDB
description: Dynamic DynamoDB for monitoring and scaling Amazon DynamoDB
#image: /assets/media/Lamp.jpg
Categories: [AWS]
tags: [AWS, dynamic-dynamodb, dynamodb, nosql, dijeeshpnair, devops, ]
comments: true
---
Autoscaling Amazon DynamoDB
======

Introduction
----------------
In this article, I would like to share how to deploy **dynamic-dynamodb** for managing and scaling Amazon DynamoDB NoSQL Databases.

Amazon Dynamic DynamoDB  is super fast, flexible, consistent and fully managed NoSQL database service from Amazon.  DynamoDB synchronously replicates data across three different facilities in an AWS region. This will ensure high availability and data durability. It's allow horizontal scaling manually.  But whois going to sit-down and monitor the table capacity and scale up/down whenever required.  Thanks to [Sebastian Dahlgren](https://github.com/sebdah/dynamic-dynamodb) for this wonderful tool called dynamic-dynamodb.

dynamic-dynamodb will help you to avoid manual intervention for scaling activities by monitoring and scaling up/down based on the policies we defined.

dynamic-dynamodb can be either deployed using the [cloud-formation script](https://github.com/sebdah/dynamic-dynamodb/blob/master/cloudformation-templates/dynamic-dynamodb.json) they provided or manually inside your private subnet by following the instructions given bellow.

How it works
----------------

Dynamic DynamoDB is a small script that will monitor DynamoDB in specified intervals and scale up / down the DynamoDB table as per the policy we configured.


Architecture
------

<p style="text-align:justify;">
Here is a sample architecture diagram. </P>

<br>
![Architecture Diagram](assets/media/dynamic_dynamoDB.jpg)

<br>
<p style="text-align:justify;">

 In this scenario, I do have a custom VPC created for Web Application with 2 Public Subnets in each availability zone for Load Balancers and bastion nodes. Below that two private subnets in each availability zone for Web Service EC2 instances,  RDS multi AZ deployment for database services and below that we do have two subnets ( 1 in each AZ) for management nodes and I have configured a dynamic-dynamodb instance inside an autoscaling group with minimum 1 node running at time.  
</p> <br>
<br>
 - dynamic-dynamodb instance is running securely in private subnets.

 - dynamic-dynamodb's high availability is ensured by autoscaling group.


 In progress.....
