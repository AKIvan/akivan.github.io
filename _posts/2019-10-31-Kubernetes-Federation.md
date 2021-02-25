---
layout: post
title:  "Steps to create Kubernetes Cluster Federation"
date:   2019-10-31
desc: "Steps to create Kubernetes Cluster Federation"
keywords: "Kubernetes, Federation, Kubernetes Cluster Federation, AWS"
categories: [HTML, Devops]
tags: [Kubernetes,Kubernetes Cluster Federation]
icon: icon-html
---

Kubernetes (K8s) is an open-source system for automating deployment, scaling, and management of containerized applications. It groups containers that make up an application into logical units for easy management and discovery.

Kubernetes Cluster Federation, which was first released in version 1.3 back in July 2016, allows you to federate multiple clusters together and then control them as a single entity using a Federation control plane. Federation supports adding clusters located in different regions within the same cloud provider network, clusters spanning across multiple cloud providers and can even include on-premise clusters. This can be achieve by providing 2 major building blocks.

So here are some reasons why to use federation

- Sync resources across clusters: Federation provides the ability to keep resources in multiple clusters in sync. For example, you can ensure that the same deployment exists in multiple clusters.

- Cross cluster discovery: Federation provides the ability to auto-configure DNS servers and load balancers with backends from all clusters. For example, you can ensure that a global VIP or DNS record can be used to access backends from multiple clusters.


Some other use cases that federation enables are:

- High Availability
- Avoiding provider lock-in
Federation is not helpful unless you have multiple clusters. 

Some of the reasons why you might want multiple clusters are:

- Low latency
- Fault isolation
- Scalability
- Hybrid cloud

This scripts, that you can find on my [Git](https://github.com/AKIvan/K8s-Federation), help you create Kubernetes Cluster on AWS in different regions and then create Federation between clusters.

## Implementation

First you need to configure the AWS Credentials or AWS Variable in the shell where you will execute the scripts. This can be done using the AWS CLI. If you don't have it, you can install it using pip **("pip install awscli --upgrade").**

For general use, the aws configure command is the fastest way to set up your AWS CLI installation.

```
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-west-2
Default output format [None]: json
``` 

Then start execute the scripts for creating the Clusters in regions. example:
**K8CUSA.sh** will create Kubernetes Cluster in USA 'us-east-1' Region.
You must have minimum 2 Clusters in order to Join them in Federations.
You can find the other two scripts for creating Cluster in Japan and  Europe (K8CJPN.sh and K8CEUR.sh).

After You create the cluster you can then execute the **K8CJoin.sh** in order to join them in cluster. If you have other cluster that you want to be in the federation you will have to add them in the script. To do this you have to add at the botom of the script another line:
```
kubefed join ${ANOTHER_CLUSTER_ALIAS} --host-cluster-context=${USA_CLUSTER_ALIAS} --cluster-context=${ANOTHER_CLUSTER_ALIAS}
```
The host cluster context is the **host cluster** hosting the components that make up the Federation control plane.

**Note:**
This is old way but usefull in order to start. Its possible that some images during the creation of cluster would not exists or are out of date, but that part is easy to fix. 

To delete the cluster, use the script that start with "DEL", (**DELK8CUSA.sh**, in order to delete USA Cluster) 

