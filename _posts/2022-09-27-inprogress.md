---
layout: post
title: "Working on..."
author: "David Peter"
categories: workingon
tags: [documentation,sample]
image: progressbar.png
---

Searx is a free and open-source metasearch engine (ie. an online information retrieval tool that uses the data of other web search engines to produce its own results) with the aim of protecting the privacy of its users. To this end, Searx does not share users' IP addresses or search history with the search engines from which it gathers results. Tracking cookies served by the search engines are blocked, preventing user-profiling-based results modification. Users can run private instances of Searx on their own computer, but there are also many public, user-run, Searx instances, some of which are available as Tor hidden services. (source: [Wikipedia](https://en.wikipedia.org/wiki/Searx).)

SearXNG is a fork of Searx. Here you can find their [Github repository](https://github.com/searxng/searxng-docker).

In this project we will create a new SearXNG instance on AWS using Docker.

## Launch an EC2 instance on AWS

In this occasion we will use the AWS CLI to spin up our virtual machine. To install or update the AWS CLI on Windows we simply run the following command:

```
C:\> msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
```

You can find how to quickly configure the basic settings of the CLI on the [AWS docs site](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html).

In order to create the instance, we run the following command:

```
C:\> aws ec2 run-instances --image-id ami-08c40ec9ead489470 --count 1 --instance-type t2.micro --key-name linux_nvirginia --security-group-ids sg-0c70db6fdbb69074c --tag-specifications ResourceType=instance,Tags=[{Key=Name,Value=searxng_server}]
```

```run-instances``` launches the specified number of instances using an AMI for which you have permissions. We have specified the following options:

- --image-id: The ID of the AMI. An AMI ID is required to launch an instance and must be specified here or in a launch template. Here we are using Ubuntu Server 22.04
- --count: Number of instances to launch.
- --instance-type: The instance type. A t2.micro will be enough
- --key-name: The name of the key pair. We use a key generated beforehand.
- --security-group-ids: The IDs of the security groups.
- --tag-specifications: The tags to apply to the resources that are created during instance launch. We give a name to our instance.

Then we will see the below output, by default in json format:

```JSON
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
```

## Install Docker Engine and Docker Compose

### We ssh to the instance, update the package information and install available upgrades of all packages

```console
ubuntu@ip-172-31-91-179:~$ sudo apt update && sudo apt upgrade -y
Hit:1 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-updates InRelease [114 kB]
Get:3 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-backports InRelease [99.8 kB]
Get:4 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 Packages [14.1 MB]
```

### How to install `docker` and `docker-compose`

You can find details [on this](https://docs.docker.com/engine/install/) and [this](https://docs.docker.com/compose/install/) link.

We install Docker and Docker Compose using their repository. Before to do that for the first time on a new host machine, we need to set up the Docker repository. Afterward, we can install and update Docker from the repository.

Update the apt package index and install packages to allow apt to use a repository over HTTPS:

```console
ubuntu@ip-172-31-91-179:~$ sudo apt install ca-certificates curl gnupg lsb-release
Reading package lists... Done
Building dependency tree... Done
```

Add Docker’s official GPG key:

```console
ubuntu@ip-172-31-91-179:~$ sudo mkdir -p /etc/apt/keyrings
ubuntu@ip-172-31-91-179:~$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

Use the following command to set up the repository:

```console
$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Update the apt package index

```console
ubuntu@ip-172-31-91-179:~$ sudo apt update
Hit:1 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-updates InRelease [114 kB]
Get:3 http://us-east-1.ec2.archive.ubuntu.com/ubuntu jammy-backports InRelease [99.8 kB]
Get:4 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:5 https://download.docker.com/linux/ubuntu jammy InRelease [48.9 kB]
Get:6 https://download.docker.com/linux/ubuntu jammy/stable amd64 Packages [7065 B]
Fetched 380 kB in 0s (890 kB/s)
Reading package lists... Done
```

Install the latest version of Docker Engine, containerd, and Docker Compose

```console
ubuntu@ip-172-31-91-179:~$ sudo apt install docker-ce docker-ce-cli containerd.io docker-compose
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
```

Verify that Docker Engine is installed correctly by running the `hello-world` image.

```console
ubuntu@ip-172-31-91-179:~$ sudo service docker start
ubuntu@ip-172-31-91-179:~$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete
Digest: sha256:62af9efd515a25f84961b70f973a798d2eca956b1b2b026d0a4a63a3b0b6a3f2
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
```

### Get searxng-docker

Clone the `git` repo to our machine to `/usr/local` and change the current directory to `searxng-docker`

```console
ubuntu@ip-172-31-87-211:~$ cd /usr/local
ubuntu@ip-172-31-87-211:/usr/local$ sudo git clone https://github.com/searxng/searxng-docker.git
Cloning into 'searxng-docker'...
remote: Enumerating objects: 275, done.
remote: Counting objects: 100% (36/36), done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 275 (delta 25), reused 23 (delta 23), pack-reused 239
Receiving objects: 100% (275/275), 71.87 KiB | 5.53 MiB/s, done.
Resolving deltas: 100% (153/153), done.
ubuntu@ip-172-31-87-211:/usr/local$ cd searxng-docker
ubuntu@ip-172-31-87-211:/usr/local/searxng-docker$
```

Edit the `.env` file to set the hostname and an email

```console
ubuntu@ip-172-31-87-211:/usr/local/searxng-docker$ ls -a
.   .env  .gitignore  LICENSE    docker-compose.yaml  searxng-docker.service.template
..  .git  Caddyfile   README.md  searxng
ubuntu@ip-172-31-87-211:/usr/local/searxng-docker$ sudo nano .env
```

This is the original `.env` file:

```
# By default listen on https://localhost
# To change this:
# * uncomment SEARXNG_HOSTNAME, and replace <host> by the SearXNG hostname
# * uncomment LETSENCRYPT_EMAIL, and replace <email> by your email (require to create a Let's Encrypt certificate)

# SEARXNG_HOSTNAME=<host>
# LETSENCRYPT_EMAIL=<email>
```

Uncomment the last 2 lines according to the instructions in the file

```
# By default listen on https://localhost
# To change this:
# * uncomment SEARXNG_HOSTNAME, and replace <host> by the SearXNG hostname
# * uncomment LETSENCRYPT_EMAIL, and replace <email> by your email (require to create a Let's Encrypt certificate)

SEARXNG_HOSTNAME=1.2.3.4 # your public IP
LETSENCRYPT_EMAIL=thisis@yourmail.com
```

Generate the secret key

```console
ubuntu@ip-172-31-87-211:/usr/local/searxng-docker$ sudo sed -i "s|ultrasecretkey|$(openssl rand -hex 32)|g" searxng/settings.yml
```

Check everything is working

```console
ubuntu@ip-172-31-87-211:/usr/local/searxng-docker$ sudo docker-compose up
Creating network "searxng-docker_searxng" with the default driver
Creating volume "searxng-docker_caddy-data" with default driver
Creating volume "searxng-docker_caddy-config" with default driver
Pulling caddy (caddy:2-alpine)...
2-alpine: Pulling from library/caddy
213ec9aee27d: Pull complete
fd0c7d01ba8a: Pull complete
```

Run SearXNG in the background

```console
ubuntu@ip-172-31-87-211:/usr/local/searxng-docker$ sudo docker-compose up -d
Starting caddy   ... done
Starting searxng ... done
Starting redis   ... done
```

Let's see the running docker containers with `docker ps`

```console
ubuntu@ip-172-31-28-42:/usr/local/searxng-docker$ sudo docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED         STATUS         PORTS                      NAMES
605e43865523   searxng/searxng:latest   "/sbin/tini -- /usr/…"   7 seconds ago   Up 5 seconds   127.0.0.1:8080->8080/tcp   searxng
1957c29e0449   caddy:2-alpine           "caddy run --config …"   7 seconds ago   Up 6 seconds                              caddy
0576430ce0a6   redis:alpine             "docker-entrypoint.s…"   7 seconds ago   Up 5 seconds   6379/tcp                   redis
```



