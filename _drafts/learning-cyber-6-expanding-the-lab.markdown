---
layout: post
title: 'Learning Cyber 6: Expanding the Lab'
tags:
- learningcyber
---

While the lab we've setup in the past few posts is sufficent for most tasks, it's going to be a work in progress. In today's post we are going to expand the lab to include a few new elements.

<!--kg-card-begin: markdown-->
- [Flare VM](https://www.fireeye.com/blog/threat-research/2017/07/flare-vm-the-windows-malware.html) (Windows Malware Analysis Virtual Machine)
- [Commando VM](https://www.fireeye.com/blog/threat-research/2019/03/commando-vm-windows-offensive-distribution.html) (Windows Penetration Testing Virtual Machine)
- [ELK Stack](https://www.elastic.co/elk-stack) (Centralized Logging System Running on Ubuntu)
<!--kg-card-end: markdown--><!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->
# Flare VM

Flare is a distribution of packages for a Windows VM that help with malware analysis. Mandiant did an awesome job with this setup and I highly recommend it. I've mentioned it before, but I decided to include it here for completeness.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

To start with, you'll need a Windows VM. As mentioned before, you can use the developer VM's available [here](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/). Choose the latest Windows 10 VM.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Import the VM into your hypervisor (If you don't remember how, you can visit [Learning Cyber 1](https://d3fiant.io/learningcyber-1/). Once the VM has been imported, go ahead and boot it up.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Inside your Windows VM, open a web-browser and visit [https://github.com/fireeye/flare-vm](https://github.com/fireeye/flare-vm). Follow the instructions on the github site. They are straightforward to follow and you shouldn't have any problems. Note, that the install will take a long time so go ahead and grab a cup of coffee. Once it completes, take a snapshot and shut the VM down.

<!--kg-card-end: markdown--><!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->
# Commando VM
<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Commando is another distribution of Windows packages setup by Mandiant. In contrast to the Flare-VM, Commando is focused on offensive security tools. Generally I prefer Kali, but it's very useful to have a Windows environment for engagements.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

To get started, go ahead and download another Windows VM. Import it and start it up. Once it's running, head to [https://github.com/fireeye/commando-vm](https://github.com/fireeye/commando-vm). Again, follow the instructions and prepare a cup of coffee. Once it completes, take a snapshot and shutdown the VM.

<!--kg-card-end: markdown--><!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->
# ELK Stack

ELK is an acronym for "Elasticsearch, Logstach, and Kabana". Elasticsearch is a search engine for log files. Logstash is the pipe that feeds the ELK Stack. It collects logs, parses them, and sends them on to elasticsearch. Lastly, Kabana sits at the top providing visualization for your data.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

To build our ELK setup, we are going to use the following technologies:

- Ubuntu
- Docker
- ELK Docker Image
- Windows VM
- Winlogbeat
- Sysmon
<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

While the ELK stack is an fantastic tool, it isn't much use without logs. To get logs, we are going to make use of [Sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon), a Microsoft utility designed to provide more logging options for Windows. To get the Sysmon logs fro the Windows system to the ELK stack, we'll us [Winlogbeat](https://www.elastic.co/products/beats/winlogbeat). Winlogbeat will read the logs from the system and send them to our ELK server.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->
## Ubuntu VM Creation

To get started, go ahead and download an Ubuntu server image from [here](https://www.ubuntu.com/download/server). Select the latest LTS version and download it. LTS stands for Long Term Support. LTS versions of Ubuntu are supported for longer periods and are considered stable.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Once the Ubuntu ISO is downloaded. Switch over to VMWare (Workstation or Fusion) and create a new Virtual Machine by selecting _File-\>New_.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/05/Screen-Shot-2019-04-26-at-9.08.57-AM.png" class="kg-image"><figcaption>Creating a New Virtual Machine</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/05/Screen-Shot-2019-04-26-at-9.09.45-AM.png" class="kg-image"><figcaption>Creating a New Ubuntu VM</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

As you go through the prompts, notice the option for Linux _Easy Install_. We'll go ahead and use that to make things go a bit quicker.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/05/Screen-Shot-2019-04-26-at-9.12.19-AM.png" class="kg-image"><figcaption>Linux Easy Install</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Once the import is complete, boot into the VM.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/05/Screen-Shot-2019-05-10-at-2.09.58-PM.png" class="kg-image"><figcaption>Ubuntu Login Prompt</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->
## Elk Installation

Go ahead and log-in using the credentials you setup during _Easy Install_. Once in run the following commands to make sure that everything is up to date.

    sudo apt update
    sudo apt upgrade

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Next, we'll need to install Docker. Follow the instructions [here](https://docs.docker.com/install/linux/docker-ce/ubuntu/).

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Finally, we'll download the Elk docker image. More information is available [here](https://elk-docker.readthedocs.io).

    sudo docker pull sebp/elk

<!--kg-card-end: markdown-->