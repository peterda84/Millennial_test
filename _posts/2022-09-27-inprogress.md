---
layout: post
title: "Working on..."
author: "David Peter"
categories: workingon
tags: [documentation,sample]
image: progressbar.png
---

Searx is a free and open-source metasearch engine (ie. an online information retrieval tool that uses the data of other web search engines to produce its own results) with the aim of protecting the privacy of its users. To this end, Searx does not share users' IP addresses or search history with the search engines from which it gathers results. Tracking cookies served by the search engines are blocked, preventing user-profiling-based results modification. Users can run private instances of Searx on their own computer, but there are also many public, user-run, Searx instances, some of which are available as Tor hidden services. 

SearXNG is a fork of Searx. (source: [Wikipedia](https://en.wikipedia.org/wiki/Searx).)

In this project we will create a new SearXNG instance on AWS using Docker.

## Launch an EC2 instance on AWS

In this occasion we will use the AWS CLI to spin up our virtual machine.

```pwsh
C:\Users\54911>aws ec2 run-instances --image-id ami-08c40ec9ead489470 --count 1 --instance-type t2.micro --key-name linux_nvirginia --security-group-ids sg-0c70db6fdbb69074c --tag-specifications ResourceType=instance,Tags=[{Key=Name,Value=searxng_server}]

{
    "Groups": [],
    "Instances": [
        {
            "AmiLaunchIndex": 0,
            "ImageId": "ami-08c40ec9ead489470",
            "InstanceId": "i-0f33d62194319c5b2",
            "InstanceType": "t2.micro",
            "KeyName": "linux_nvirginia",
            "LaunchTime": "2022-09-27T21:28:29+00:00",
            "Monitoring": {
                "State": "disabled"
            },
            "Placement": {
                "AvailabilityZone": "us-east-1a",
                "GroupName": "",
                "Tenancy": "default"
            },
            "PrivateDnsName": "ip-172-31-31-3.ec2.internal",
            "PrivateIpAddress": "172.31.31.3",
            "ProductCodes": [],
            "PublicDnsName": "",
            "State": {
                "Code": 0,
                "Name": "pending"
            },
...
```
