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

![image1](https://github.com/peterda84/peterda84.github.io/blob/dev/assets/img/boinc/launchec2_step1.png)

### Select the image

![image2](https://github.com/peterda84/peterda84.github.io/blob/dev/assets/img/boinc/launchec2_step2.png)

### Select the instance type and the key par

![image3](https://github.com/peterda84/peterda84.github.io/blob/dev/assets/img/boinc/launchec2_step3.png)

### Select security groups

![image4](https://github.com/peterda84/peterda84.github.io/blob/dev/assets/img/boinc/launchec2_step4.png)

### Configure storage

![image5](https://github.com/peterda84/peterda84.github.io/blob/dev/assets/img/boinc/launchec2_step5.png)

### Our instance is being launched

![image6](https://github.com/peterda84/peterda84.github.io/blob/dev/assets/img/boinc/launchec2_step6.png)

### Instance is up and running

![image7](https://github.com/peterda84/peterda84.github.io/blob/dev/assets/img/boinc/launchec2_step7.png)


## Installing and configuring BOINC
