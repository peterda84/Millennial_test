---
layout: post
title: "Working on..."
author: "David Peter"
categories: workingon
tags: [documentation,sample]
image: progressbar.png
---

BOINC (Berkeley Open Infrastructure for Network Computing) is an open-source system for volunteer computing. It is a high-performance distributed computing platform. It brings together as many personal computers that all together would be one of largest processing capability in the world compared with individual supercomputers. In essence, BOINC is software that can exploit the unused CPU and GPU cycles on a computer to do scientific computing — what one individual does not utilize of their computer, BOINC uses.

There are about 30 science projects that use BOINC in many areas. They investigate diseases, study climate change, discover pulsars, and do many other types of scientific research. (source: Wikipedia and BOINC homepage)

In this project we will create an EC2 instance on Amazon AWS, install the BOINC client on it and attach to a science project.

## Launch an EC2 instance on AWS

In this occasion we will use the AWS Management Console to spin up our virtual machine.

### Name the instance

We use a nice and descriptive name for the machine.

![image1](/assets/img/boinc/step1.png)

### Select the image

Let's use Ubuntu 22.04 LTS.

![image2](/assets/img/boinc/step2.png)

### Select the instance type and the key par

For instance type we select t2.micro since it is elegible as part of the free tier. A key pair is recommended to use to connect to the instance securely.

![image3](/assets/img/boinc/step3.png)

### Select security groups

In order to SSH the instance and be able to connect to it via the BOINC manager (see later), we create and attach a security group, allowing all incoming traffic from our IP address.

![image4](/assets/img/boinc/step4.png)

### Configure storage

It is ok to leave the standard setting of 8GB SSD volume.

![image5](/assets/img/boinc/step5.png)

### Our instance is being launched

Just a few moments more...

![image6](/assets/img/boinc/step6.png)

### Instance up and running

Now we are ready to install the BOINC client.

![image7](/assets/img/boinc/step7.png)


## Installing and configuring BOINC

'''
ubuntu@ip-172-31-29-129:~$ sudo systemctl status boinc-client
● boinc-client.service - Berkeley Open Infrastructure Network Computing Client
    Loaded: loaded (/lib/systemd/system/boinc-client.service; enabled; vendor preset: enabled)
    Active: active (running) since Thu 2022-09-01 22:34:16 UTC; 16h ago
      Docs: man:boinc(1)
  Main PID: 1791 (boinc)
  Tasks: 4 (limit: 1143)
  Memory: 208.4M
  CPU: 16h 11min 5.779s
'''
