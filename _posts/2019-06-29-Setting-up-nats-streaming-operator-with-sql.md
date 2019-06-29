---
layout: post
title: Setting up NATS Streaming Operator with SQL Storage in EKS
description: Setting up NATS Streaming Operator with SQL Storage in EKS
#image: /assets/media/Lamp.jpg
Categories: [AWS]
tags: [devops, sysops, aws, EKS, kubernetes, containerization, dijeeshpnair, nats-io, nats-streaming, nats-operator, nats-streaming-operator ]
comments: true
---

### Introduction

<br>

![NATS](assets/media/blogs-nats-io.jpg)


#### **NATS-IO**
<br>

NATS.io is a simple, secure and high performance open source messaging system for cloud native applications, IoT messaging, and microservices architectures.

<br>

#### nats server and nats-streaming-server

<br>

NATS is available in two interoperable modules, the core NATS platform - the NATS server (executable name is gnatsd) referred to simply as NATS and NATS Streaming (executable name is nats-streaming-server)

- Basic NATS Server is designed for high performance and simplicity.
- NATS Server doesn’t provide a persistent store for the messages that you publish over the NATS.
- NATS Streaming comes with a persistent store for having a log for the messages that publish over the NATS server
- NATS Streaming is not a separate server.
- NATS Streaming embeds a NATS server as the messaging server, and provides an extra capability of having a persist logs to be used for event streaming systems.

<br>

#### nats-operator

<br>

https://github.com/nats-io/nats-operator

- nats-operator manages NATS clusters atop Kubernetes
- nats-operator automates the creation and administration of Nats cluster
- nats-operator provides a NatsCluster Custom Resources Definition (CRD) that models a NATS cluster

This CRD allows for specifying the desired size and version for a NATS cluster, as well as several other advanced options

<br>

#### nats-streaming-operator

<br>

https://github.com/nats-io/nats-streaming-operator

- nats-streaming-operator makes available a NatsStreamingCluster Custom Resources Definition that creates a NATS Streaming Cluster on top of a K8S Cluster.
- nats-streaming-operator can also be used to manage instances backed by a SQL store. In this mode, only a single replica will be created

To use DB store support it is needed to include the DB credentials within the NATS Streaming configuration and mount it as a volume.


<br>

#### **Setup Instructions**

<br>

Setup Instructions and deployment files are available in this [Github Repository](https://github.com/dijeesh/nats-streaming-operator-with-sql-backend)