---
layout: post
title: Setting up Kubectl for Amazon EKS
description: Setting up Kubectl for Amazon EKS
#image: /assets/media/Lamp.jpg
Categories: [AWS]
tags: [devops, sysops, aws, EKS, kubernetes, containerization, dijeeshpnair, kubectl ]
comments: true
---

### **1. Install kubectl for Amazon EKS**

Follow Kubernetes [Documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/) to install Kubectl 

#### MacOS
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin/kubectl

kubectl version
```

#### Linux

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin/kubectl

```

<br>

### **2. Install the Latest AWS CLI**

You will need version 1..16.156 or greater for working with kubectl for Amazon EKS

#### Prerequisites

Python v3 installed and the pip commands shown use the pip3 version.

#### Install AWS CLI using pip

```
pip3 install awscli --upgrade --user
```
Update $PATH

#### Install Using the Bundled Installer

```
curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
unzip awscli-bundle.zip
sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
```

<br>

### **3. Configure AWS CLI**

Get Access Key and Secret Access Key for Production EKS Cluster user and configure aws cli by running

```
aws configure
```

We are using us-east-1 Region.

<br>

### **4. Configure IAM Authenticator** 

Amazon EKS uses IAM to provide authentication to your Kubernetes cluster through the AWS IAM Authenticator for Kubernetes.

#### MacOS

```
curl -o aws-iam-authenticator https://amazon-eks.s3-us-west-2.amazonaws.com/1.12.7/2019-03-27/bin/darwin/amd64/aws-iam-authenticator

chmod +x ./aws-iam-authenticator

mkdir -p $HOME/bin && cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$HOME/bin:$PATH

echo 'export PATH=$HOME/bin:$PATH' >> ~/.bash_profile

```

#### Linux

```
curl -o aws-iam-authenticator https://amazon-eks.s3-us-west-2.amazonaws.com/1.12.7/2019-03-27/bin/linux/amd64/aws-iam-authenticator


chmod +x ./aws-iam-authenticator

mkdir -p $HOME/bin && cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$HOME/bin:$PATH


echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc

```

<br>

### **5. Create kubeconfig file**

```
aws eks --region region update-kubeconfig --name cluster_name
```

<br>

### **6. Testing configuration**

```
kubectl get svc
```



