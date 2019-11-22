---
title: Resources
theme: jekyll-theme-slate
layout: default
---
<nav>
	<ul>
   		{% for item in site.data.sitelist.docs %}
      			<a href="{{ item.url }}">{{ item.title }}</a> - 
   		{% endfor %}
	</ul>
</nav>
  
Collection of resources on a variety of topics that I've found useful over the years (Continuously updated).

----

## Tools - Meta Packages

- [Kali Linux](https://www.kali.org/downloads/)
  - Linux Distribution full of various security tools useful for penetration testing
- [Remnux](https://remnux.org/)
  - Linux Distribution with a number of security tools, useful for DFIR.
- [Flare VM](https://www.fireeye.com/blog/threat-research/2017/07/flare-vm-the-windows-malware.html)
  - Metapackage for Windows DFIR tools
- [Pentesters Framework](https://github.com/trustedsec/ptf)
  - Python Script to auto-magically handle installing and managing various security tools (mostly for pentesting)

----

## Security Sources for Practice (CTFs, ect.)**

- [Hack the Box](https://www.hackthebox.eu/)
  - Capture the Flag site with vpn access to vulnerable network, crackme challenges, and reversing challenges.
- [Vulnhub](https://www.vulnhub.com/)
  - Website with a ton of vulnerable virtual machines for you to practice on
- [Metasploitable](https://sourceforge.net/projects/metasploitable/)
  - Virtual Machine with many vulnerabilities that have exploits available in metasploit.
- [TryHackMe](https://tryhackme.com/)

----

## Windows Tools

- [Windows VMs](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/)
  - Variety of windows virtual machines. They stop working after 30 days so create a snap shot and just revert. Password is _Passw0rd!_
- [SysInternals](https://docs.microsoft.com/en-us/sysinternals/downloads/)
  - Suite of useful windows tools.
- [SysMon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)
  - Part of the sysinternals suite. A tool to add more robust logging for windows. Especially useful with a good config and logs being sent to a log management system like ELK, Splunk, etc.
  - [Good Sysmon Config](https://github.com/SwiftOnSecurity/sysmon-config)
- [Process Hacker](https://processhacker.sourceforge.io/)
  - Like task manager in windows but more useful.
 
 ----
  
## Podcasts

- [Paul's Security Weekly](https://securityweekly.com/)
- [Security Now](https://twit.tv/shows/security-now)
- [Red Team Podcast](https://redteams.net/podcast)
- [Sans Internet Storm](https://isc.sans.edu/)
- [Troy Hunts Weekly](https://www.troyhunt.com/my-weekly-updates-are-now-available-as-an-audio-podcast/)

----

## Twitter: Security Professionals

- @arstechnica
- @briankrebs
- @chrisjohnriley
- @cyb3rops
- @darkreading
- @exploitdb
- @gcluley
- @g0tmi1k
- @hdmoore
- @jeremiahg
- @ICSCERT
- @LG\_Cyber
- @mcAfee\_labs
- @nakedsecurity
- @owasp
- @pentestmonkey
- @pwnallthethings
- @robertmlee
- @scadahacker
- @schneierblog
- @seclists
- @securityweek
- @sophoslabs
- @threatpost
- @trendlabs
- @tripwire
- @vcuinfosec

----

## News Sources

- [Nuzzel](https://blog.nuzzel.com/)
  - I'm use twitter combined with Nuzzel as a pretty effective notification system for cyber news. Nuzzel connects to your twitter account and notifies you when 3 (adjustable threshold) or more people that you follow retweet the same thing.
- [Unsupervised Learning](https://danielmiessler.com/subscribe/)
  - Bi-Weekly Newsletter for the top security news and technical news

----

## Malware Analysis and Reverse Engineering Links

- [Linux Reverse Engineering Exercises](https://github.com/michalmalik/linux-re-101)
- [File Headers](https://www.garykessler.net/library/file_sigs.html)
- [Malware Repository](https://github.com/ytisf/theZoo)

---

## Pentesting Resources

- [Pentesting Tools Cheat Sheet](https://highon.coffee/blog/penetration-testing-tools-cheat-sheet/#introduction)
- [Cobalt Strike](https://blog.cobaltstrike.com/2015/09/30/advanced-threat-tactics-course-and-notes/)

