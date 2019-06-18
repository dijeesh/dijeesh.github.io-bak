---
layout: post
title: Setting up additional users in EKS
description: Setting up additional users in EKS
#image: /assets/media/Lamp.jpg
Categories: [AWS]
tags: [devops, sysops, aws, EKS, kubernetes, IAM, dijeeshpnair, kubectl ]
comments: true
---

# **EKS Authentication - Summary**

![EKSAUTH](assets/media/eks_auth_diagram.png)


- Amazon EKS uses IAM to provide authentication to your Kubernetes cluster.

- It uses aws eks get-token command, available in version 1.16.156 or greater of the AWS CLI, or the AWS IAM Authenticator for Kubernetes

- And for authorization, it still relies on native Kubernetes Role Based Access Control (RBAC)

<br>

-  When you create an Amazon EKS cluster, the IAM entity user or role (for example, for federated users) that creates the cluster is automatically granted system:masters permissions in the cluster's RBAC configuration


<br>

**Here are the steps for adding additional users to the EKS Cluster**

1. Create IAM Policy
2. Create IAM User and attach the Policy
3. Update EKS aws-auth configmap
4. Create EKS Cluster Role
5. Create EKS Cluster Role Binding
6. Create IAM Access Key Pair
7. Configure Kubectl
8. Generate kubeconfig
 

<br>

##  <span style="color:red">**EKS Read-Only User**</span>


<br>

### **1. Create IAM Policy for EKS Read-Only Access**

*CLI*
```
aws iam create-policy --policy-name=eks-readonly-policy --policy-document='{"Version": "2012-10-17", "Statement": {"Sid": "45345354354", "Effect": "Allow", "Action": ["eks:DescribeCluster", "eks:ListCluster" ], "Resource": "*" }}'
```

*Console*
```
{
    "Version": "2012-10-17",
    "Statement": {
        "Sid": "45345354354",
        "Effect": "Allow",
        "Action": [
            "eks:DescribeCluster",
            "eks:ListCluster"
        ],
        "Resource": "*"
    }
}
```

<br>

### **2. Create IAM User and attach EKS Read-Only Policy**


*CLI*
```
aws iam create-user --user-name=eks-readonly-user

aws iam attach-user-policy --user-name=eks-readonly-user --policy-arn=arn:aws:iam::xxxxxxxx:policy/eks-readonly-policy
```

*Console*

IAM > User > Add User


<br>

### **3. Update EKS aws-auth configmap**


Dump existing configmap
```
kubectl -n kube-system get configmap aws-auth -o yaml  > aws-auth.yaml
```

Edit aws-auth.yaml and add additional users to **mapUsers** secion. You will need to provide the IAM User ARN and an Username to be used in Kubernetes. Better to use same username to avoid confusion.

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: aws-auth
  namespace: kube-system
data:
  mapRoles: |
    - rolearn: arn:aws:iam::xxxxxxxxxxxxx:role/XXXXXXXXXX-STA-NodeInstanceRole-XXXXXX
      username: system:node:{{EC2PrivateDNSName}}
      groups:
        - system:bootstrappers
        - system:nodes
  mapUsers: |
    - userarn: arn:aws:iam::xxxxxxxxxxxxx:user/eks-readonly-user
      username: eks-readonly-user
```

Apply aws-auth configmap

```
kubectl apply -f > aws-auth.yaml
```

<br>

## **4. Create an admin EKS ClusterRole**

Create file `eks-readonly-user-role.yaml` with following snippet and apply using kubectl

```
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: eks-readonly-user-role
rules:
- apiGroups: [ "*" ]
  resources: ["*"]
  verbs: ["*"]
- nonResourceURLs: ["*"]
  verbs: ["*"]
  ```
```
kubectl apply -f eks-readonly-user-role.yaml
```


<br>

## **5. Create an EKS ClusterRoleBinding**


Create file `eks-readonly-user-role-binding.yaml` with following snippet and apply using kubectl

```
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: eks-readonly-user-role-binding
subjects:
- kind: User
  name: eks-readonly-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: eks-readonly-user-role
  apiGroup: rbac.authorization.k8s.io
```

```
kubectl apply -f eks-readonly-user-role-binding.yaml
```

<br>

`Next steps are to be performed in User's console`


<br>


## **6. Create IAM Access Key Pair**

*CLI*

```
aws iam  create-access-key --user-name eks-readonly-user
```

*Console*

IAM > User > Security Credentials > Create access key


<br>

## **7. Configure kubectl**

Ref https://dijeesh.github.io/Setting-up-kubectl-for-EKS and setup kubectl for your local environment.

<br>

## **8 Generate kubeconfig**

```
aws eks --region xx-xxx-x update-kubeconfig --name xxx-eks-cluster
```
Replace region and cluster name.


<br>

Done, Try runnig kubectl commands and you should be now have readonly access to the EKS Cluster.



<br>

**References**

1. [Managing Cluster Authentication](https://docs.aws.amazon.com/eks/latest/userguide/managing-auth.html)
2. [Managing Users or IAM Roles for your Cluster](https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html)

<br>

Research Partner : [Sahir Abbas](https://github.com/sahirabbas-k) 