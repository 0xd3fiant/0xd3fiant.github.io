---
layout: default
title: 'Learning Cyber 2: Learning to Understand Your Tool-Sets'
date: '2019-03-20 02:08:10'
tags:
- learningcyber
---
<nav>
	<ul>
		<a href="/">Home</a> - <a href="/resources">Resources</a> - <a href="/blog">Blog</a> - <a href="/about">About</a>
	</ul>
</nav>

In [Learning Cyber 1](https://d3fiant.io/learningcyber-1/), I mentioned the importance of the _hacker_ mindset for a cybersecurity professional. Fundamental to the hacker mindset is the desire to understand how things work. In Learning Cyber 2 , we're going to focus on learning how our tool-sets work. &nbsp;These days, it's rare for people to take the time to learn the mechanics of what they're doing. We know what we need to do (_hit the report button_) and what the result will be (_report.pdf appears on our desktop_), but we have no idea how it all actually works. Understanding the _ **how** _, is crucial to the cyber professional. It allows us to troubleshoot, spot discrepancies, and improve our craft.

In this post, we'll go over a common tool that's been used in cybersecurity for years: &nbsp;[Nmap](https://nmap.org/). We'll be using [Wireshark](https://www.wireshark.org/)to examine the network traffic generated for different scenarios. The requisite tools are pre-installed in our lab environment.

<!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->
# Setup

For Part 2, we'll be making use of the lab setup described in [Part 1](https://d3fiant.io/learningcyber-partone/), specifically the Kali and Metasploitable virtual machines (VMs). Make special note that both of the virtual machines have their networking set to _Host-Only_ mode. Power-on both VMs and logon. Metasploitable's default credentials are msfadmin:msfadmin (username:password).

In the Metasploitable VM, take note of your IP address using

    ifconfig

Transition to the Kali VM and open a terminal. Check your IP address

    ifconfig

Now compare the IP addresses to ensure that they are both on the same network.

<!--kg-card-end: markdown--><!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->
# Prep

**Tools**

- [Nmap](https://nmap.org/) is a network scanning utility that allows you to discover assets and determine the status of the asset's ports.
- [Wireshark](https://www.wireshark.org/) is a network protocol analyzer.

**Terminology**  
To understand our tool-sets, we'll need to understand some basic networking concepts.

- Network Protocols are the guidelines and specifications for how applications communicate over a network. Further reading: [here](https://www.lifewire.com/definition-of-protocol-network-817949)
- TCP (Transmission Control Protocol) is a connection-oriented network protocol. When we say "connection-oriented", we're essentially saying that TCP checks to ensure that each message (packet) is successfully received. This is in contrast to UDP (User Datagram Protocol), which is a connectionless protocol. Further reading: [here](https://searchnetworking.techtarget.com/definition/TCP)
- Ports are numbers that are used to decide which application the network traffic is for. Further reading: [here](https://en.wikipedia.org/wiki/Port_(computer_networking))
<!--kg-card-end: markdown--><!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->
# Nmap Analysis

We'll begin with Nmap. In your Kali VM, open Wireshark by hitting the "Windows" key on your keyboard, typing _Wireshark_, and clicking the Wireshark icon. Click _Ok_ if you get a _Lua: Error duing loading:_ prompt.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/wireshark_start.PNG" class="kg-image"><figcaption>Wireshark Welcome Screen</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Click the blue fin on the top left (under File) to start a packet capture. Now that we have a packet capture going on, open a terminal to run Nmap. We'll start by scanning our Metasploitable machine with Nmap using mostly default Nmap options. Use the command below, but change 192.168.90.132 to the address of your Metasploitable VM.

    nmap -p 21 192.168.90.132

The _-p 21_ option specifies that we only want to scan port 21 on the Metasploitable VM.

When the command completes you'll see _Nmap done:_. Go ahead and stop your Wireshark capture by clicking the red square on the top left.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

At this point, we've captured the traffic for the Nmap scan. Unfortunately, there may be quite a bit of other traffic captured. No problem. We can filter the data by using the following _Display Filter_. Make sure to replace _192.168.90.137_ with the address of your Kali VM and _192.168.90.132_ with the address of your Metasploitable VM.  
`ip.addr == 192.168.90.137 && ip.addr == 192.168.90.132`  
The above command displays packets that contain both the ip address _192.168.90.137_ and _192.168.90.132_ (The && makes sure both are present). For more information on Wireshark display filters, click [here](https://wiki.wireshark.org/DisplayFilters). You should see an output similar to below.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/wireshark_nmap1.png" class="kg-image"><figcaption>Wireshark output for Nmap scan of port 21 with default options</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

From the image above, you should see 3 packets. Remember from above that TCP is a _connection-oriented_ protocol. To establish a connection, the TCP protocol performs an initial handshake. The process involves three messages and is often referred to as the _three-way handshake_. The messages for the handshake are denoted below.

- _SYN_ -\> From Sender
- _SYN ACK_ -\> From Receiver
- _ACK_ -\> From Sender

In the image above, we see a _SYN_ packet followed by a _SYN ACK_ packet (Examine the info portion of each packet). Interestingly, the last packet does **not** show an _ACK_ packet. Instead, it shows a _RST_ packet. _RST_ is another type of message used by the TCP protocol. A _RST_ message is used to signal that an unexpected message was received, which essentially cancels the connection.

So why do we see a _RST_ message instead of the expected _SYN ACK_? Remember that the purpose for our scan is to check the status of a port. The _SYN ACK_ sent by port 21 on Metasploitable signals that the port is open. At this point, Nmap has the information it needs and doesn't need the connection anymore so it cancels the it. Let's move on and examine some traffic for other port states.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

**Different Port States**  
For this portion, we're going to examine traffic destined to 3 different ports: 19, 21, and 22. Go back to your Metasploitable VM and enable the firewall. Type in the _msfamin_ password when prompted.

    sudo ufw enable

Now allow port 19 through the firewall

    sudo ufw allow 19

Finally allow port 21

    sudo ufw allow 21

At this point, the Metapsloitable VM has all ports, except for 19 and 21, blocked by the firewall. Let's go back to the Kali VM and perform another scan. Go ahead and restart your Wireshark capture by clicking the blue fin button on the top left. When prompted to _Continue Without Saving_ click _Continue_.

Run a new Nmap scan against ports 19, 21, and 22. Remember to replace the _192.168.90.139_ address with the address of your Metasploitable VM.

    nmap -p 19,21,22 192.168.90.139

Once the scan completes, go back to Wireshark and stop the capture (Red Stop Sign). Make sure that you still have the display filter from above in your capture. If you don't see any output in Wireshark, double check that your IP addresses haven't changed for some reason.

At this point you should see something similar to the image below.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/nmap_3_ports.png" class="kg-image"><figcaption>Wireshark output for Nmap scan of ports 19, 21, and 22</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

In the above image, we can see 7 packets. The traffic for port 21 should look identical to our last scan. The port information is in the info section of each packet. You should see two numbers separated by an _-\>_. The first number is the source port, while the second number is the destination port. We're scanning port 21 on the Metasploitable VM, but the source port on the Kali VM will be something completely different, a high port that is not currently in use.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Let's look at the packets destined for port 22 next. As you can see, the Kali VM sends a _SYN_ packet to port 22 (Just as it did for port 21). Interestingly, the Metasploitable VM responds with a _RST ACK_ message. It does this to signal that there isn't a service (ie application) listening (think using) port 19. Since there isn't a service using port 19, the Metasploitable VM acknowledges that it received a message (the _ACK_ part) but tells the sender that it doesn't expect input on that port (the _RST_ part).

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Lastly, let's look at the 2 packets destined for port 22. Notice that both packets are sent from the Kali VM and that we don't see a response from the Metasploitable VM. Remember that we set the firewall on the Metasploitable VM to block everything but ports 19 and 21. Since port 22 is being blocked, the Metasploitable VM doesn't reply to the _SYN_ message sent by the Kali VM. When it doesn't receive a reply, Nmap attempts to send a second _SYN_ message. If it doesn't receive a response a second time, it marks the port as "Filtered" meaning that it believes a firewall is blocking the messages.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

**Nmap Connection Options**

For the next exercise, we'll examine a few of the Nmap options. Specifically, we'll look at the different types of connections that Nmap can make.

To begin, start a new Wireshark capture. Next, perform a new Nmap scan. Remember to substitute the address below for that of your Metasploitable VM.

    nmap -sS -p 21 192.168.90.140

Once the scan completes, stop the Wireshark capture. We'll use the same display filter as before.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/nmap3-1.png" class="kg-image"><figcaption>Nmap SYN scan of port 21</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

If you look closely, you'll notice that the packets above look very similar to our initial scan of port 21. That's because Nmap uses a SYN scan by default. Let's examine another scan technique: the full connect scan. Start a new Wireshark capture and execute the following Nmap scan.

    nmap -sT -p 21 192.168.90.140

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/nmap4.png" class="kg-image"><figcaption>Nmap full connect scan of port 21</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Notice that the full connect scan resulted in 4 messages.

- _SYN_ -\> From Kali
- _SYN ACK_ -\> From Metasploitable
- _ACK_ -\> From Kali
- _RST ACK_ -\> From Kali

We see that the first two packets are similar to the _SYN_ scan that we performed earlier. Next the Kali VM sends an _ACK_ message. Followed by a _RST ACK_. In a full connect scan, Nmap goes through the entire 3-way handshake before ending the connection. Once the connection is established, Nmap closes the connection with _RST ACK_.

Obviously, a full connect scan results in more traffic generated, which takes more time for the scan to complete and generates more noise. It does, however, offer a significant benefit. Since services expect traffic in a specific way (i.e. the completion of the 3-way handshake), some don't handle _SYN_ scans very well. In fact, there are some services that can completely crash when a _SYN_ scan is performed against them. For this reason, it's often safer to run a full connect scan. This is especially true in environments with older systems or software.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

**Nmap Service Scans**  
Before we leave Nmap, we'll examine one more option: the service scan. Start a new Wireshark capture and perform the following Nmap scan.

    nmap -sV -p 21 192.168.90.140

Once the scan completes, stop the Wireshark capture.

The Nmap service scan (-sV) attempts to perform a service discovery on each port that it discovers is open. See below for the difference between a regular Nmap scan and one with service discovery.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/nmap-terminal-service.png" class="kg-image"><figcaption>Difference between default Nmap Scan of port 21 and a service scan</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Now let's look at the Wireshark Capture for the Nmap service scan.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card"><img src="/content/images/2019/03/nmap_21_service.png" class="kg-image"></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

As you can see from Wireshark capture. Nmap has generated a lot more traffic than our first scan. To start with, look at the first 3 packet. Notice the message types:

- _SYN_
- _SYN ACK_
- _RST_

Looks similar to the Nmap _SYN_ scan right? It makes sense when you think about it. Before Nmap performs the service discovery, it checks to see if the port is open. Once it sees that the port is open, it moves on to the discovery portion.

Let's move on to the next three messages. Again, look at the message types:

- _SYN_
- _SYN ACK_
- _ACK_

Now that Nmap knows that the port is open, it connects to the port to perform the service discovery. After the 3-way handshake is complete, you'll notice that the following packet's protocol is different. All of the previous packets show TCP for the protocol type, while this packet shows FTP. Not that the TCP connection is established, the SSH server sends an FTP protocol message with some information about the FTP server. For this scan, we get all of the "service" information from this packet.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Let's move on to the following two packets (both sourcing from our Kali VM).

- _ACK_
- _FIN ACK_

At this point, Nmap has received the information that it needs and acknowledges the FTP packet with an _ACK_ message. Then it proceeds to terminate the connection with _FIN, ACK_ message. Up until this point, we haven't seen a _FIN_ message. A _FIN_ message is used to gracefully terminate the connection. It differs from _RST_ in a key way. _RST_ messages signal an immediate termination, while _FIN_ signals an intention to terminate. When a _FIN_ message is sent, the sender will still interact with the destination service until the session is terminated.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

The messages for the remaining packets are denoted below:

- _FTP Oops_ -\> From Metasploitable
- _RST_ -\> From Kali
- _FTP No Data_ -\> From Metasploitable
- _RST_ -\> From Kali
- _FTP No Response_ -\> From Metasploitable
- _RST_ -\> From Kali

To start with, the FTP service responds to the _FIN ACK_ sent from the Kali VM with an _FTP_ packet that denotes a _500 Oops_ message. This doesn't seem like traffic we would expect and from a quick Google search, it appears to be a bug in the version of vsftp that Metasploitable is running. Regardless, since Nmap didn't receive the expected response to a _FYN ACK_ message (which would be a _FYN ACK_ sent from the Metasploitable VM), it sends a _RST_ message. The Metasploitable VM's FTP server, responds two more times with odd messages and Kali continues to send _RST_ messages until the FTP server on Metasploitable stops sending messages.

Compare this to the image below which shows a service scan for the SSH server on the Metasploitable VM. Notice that the SSH server on the Metasploitable VM responds to the _FIN ACK_ sent by the Kali VM with a _FIN ACK_ of its own. Kali responds one last time with an _ACK_ and the session completes.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/wireshark_ssh_service_scan.png" class="kg-image"><figcaption>Wireshark output for Nmap service scan against SSH</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->
# Closing

In this post, we've covered a number of Nmap's options, but we've only examined a fraction of it's functionality. Hopefully, you'll use the techniques discussed here to explore some of Nmap's other functionality and continue with learning other tools that you use in your day-to-day. Thanks for reading.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->
# Additional Resources

- [Definitive Guide to Nmap](https://www.comparitech.com/net-admin/the-definitive-guide-to-nmap/)
- [Nmap Book](https://nmap.org/book/toc.html)
<!--kg-card-end: markdown-->
