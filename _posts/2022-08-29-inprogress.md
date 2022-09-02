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

```pwsh
PS C:\Users\54911> ssh -i "linux_nvirginia.pem" ubuntu@ec2-1-2-3-4.compute-1.amazonaws.com
The authenticity of host 'ec2-1-2-3-4.compute-1.amazonaws.com (1.2.3.4)' can't be established.
ECDSA key fingerprint is SHA256:+N8HEg9hS+v/gd3PLalPp4wefft43FAASw23rkVsg7s3o.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'ec2-1-2-3-4.compute-1.amazonaws.com,1.2.3.4' (ECDSA) to the list of known hosts.
...
```

```console
ubuntu@ip-172-31-25-132:~$ sudo apt update && sudo apt upgrade -y
Hit:1 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-updates InRelease [114 kB]
Get:3 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-backports InRelease [99.8 kB]
Get:4 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 Packages [14.1 MB]
...
```

```console
ubuntu@ip-172-31-25-132:~$ sudo apt install boinc-client -y
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libboinc7 libxss1 x11-common
Suggested packages:
  boinc-manager boinc-client-opencl boinc-client-nvidia-cuda x11-xserver-utils
The following NEW packages will be installed:
  boinc-client libboinc7 libxss1 x11-common
...
```

```console
ubuntu@ip-172-31-25-132:~$ sudo systemctl status boinc-client
○ boinc-client.service - Berkeley Open Infrastructure Network Computing Client
     Loaded: loaded (/lib/systemd/system/boinc-client.service; disabled; vendor preset: enabled)
     Active: inactive (dead)
       Docs: man:boinc(1)
```

```console
ubuntu@ip-172-31-25-132:~$ sudo systemctl start boinc-client
ubuntu@ip-172-31-25-132:~$ sudo systemctl enable boinc-client
Synchronizing state of boinc-client.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable boinc-client
Created symlink /etc/systemd/system/multi-user.target.wants/boinc-client.service → /lib/systemd/system/boinc-client.service.
```

```console
ubuntu@ip-172-31-25-132:~$ sudo systemctl status boinc-client
● boinc-client.service - Berkeley Open Infrastructure Network Computing Client
     Loaded: loaded (/lib/systemd/system/boinc-client.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2022-09-02 17:45:27 UTC; 26s ago
       Docs: man:boinc(1)
   Main PID: 1223 (boinc)
      Tasks: 2 (limit: 1143)
     Memory: 12.3M
        CPU: 450ms
     CGroup: /system.slice/boinc-client.service
             └─1223 /usr/bin/boinc

Sep 02 17:45:28 ip-172-31-25-132 boinc[1223]: 02-Sep-2022 17:45:28 [---]    don't use GPU while active
Sep 02 17:45:28 ip-172-31-25-132 boinc[1223]: 02-Sep-2022 17:45:28 [---]    suspend work if non-BOINC CPU load exceeds 25%
Sep 02 17:45:28 ip-172-31-25-132 boinc[1223]: 02-Sep-2022 17:45:28 [---]    (to change preferences, visit a project web s>
Sep 02 17:45:28 ip-172-31-25-132 boinc[1223]: 02-Sep-2022 17:45:28 [---] Setting up project and slot directories
Sep 02 17:45:28 ip-172-31-25-132 boinc[1223]: 02-Sep-2022 17:45:28 [---] Checking active tasks
Sep 02 17:45:28 ip-172-31-25-132 boinc[1223]: 02-Sep-2022 17:45:28 [---] Setting up GUI RPC socket
Sep 02 17:45:28 ip-172-31-25-132 boinc[1223]: 02-Sep-2022 17:45:28 [---] Warning: GUI RPC password is empty.  BOINC can b>
Sep 02 17:45:28 ip-172-31-25-132 boinc[1223]: 02-Sep-2022 17:45:28 [---] Checking presence of 0 project files
Sep 02 17:45:28 ip-172-31-25-132 boinc[1223]: 02-Sep-2022 17:45:28 [---] This computer is not attached to any projects
Sep 02 17:45:28 ip-172-31-25-132 boinc[1223]: 02-Sep-2022 17:45:28 Initialization completed
lines 1-21/21 (END)
```

```console
ubuntu@ip-172-31-25-132:~$ sudo nano /etc/boinc-client/remote_hosts.cfg
```

```
GNU nano 6.2                                /etc/boinc-client/remote_hosts.cfg *                                        
# This file contains a list of hostnames or IP addresses (one per line)
# of remote hosts, that are allowed to connect and to control the local
# BOINC core client via GUI RPCs.
# Lines beginning with a # or a ; are treated like comments and will be
# ignored.
#
#host.example.com
#192.168.0.180
4.3.2.1   #This is your IP 
```

```console
ubuntu@ip-172-31-25-132:~$ sudo nano /etc/boinc-client/gui_rpc_auth.cfg
```

```
GNU nano 6.2                                /etc/boinc-client/gui_rpc_auth.cfg *                                        
kGE9dfwff322jna23   #This is your password
```


```console
ubuntu@ip-172-31-25-132:~$ sudo systemctl restart boinc-client
ubuntu@ip-172-31-25-132:~$
```


```console
ubuntu@ip-172-31-25-132:~$ sudo boinccmd --project_attach http://einstein.phys.uwm.edu/ YOUR_ACCOUNT_KEY
ubuntu@ip-172-31-25-132:~$
```

```console
ubuntu@ip-172-31-25-132:~$ htop
```

