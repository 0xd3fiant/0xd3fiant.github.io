---
layout: post
title: 'Learning Cyber 4: Introduction to Malware Analysis Part 2'
date: '2019-03-31 21:27:52'
tags:
- learningcyber
- reversing_and_malware
---

In [Learning Cyber 3](https://d3fiant.io/learningcyber-3/), we started analyzing a sample of the _Jigsaw_ malware. Now, we'll continue to explore _Jigsaw_ using _Dynamic Analysis_ in the Windows environment that we setup in the last post.

<!--kg-card-end: markdown--><!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->
# VM Prep

Before we begin, make sure that the Windows VM has the network adapter disabled:  
_Settings -\> Networking -\> Uncheck Connect Network Adapter_  
and that shared folders are disabled:  
_Settings -\> Sharing -\> Uncheck Enable Shared Folders_.

Also, make sure that your Windows protections are still disabled. If you have reset the VM since Learning Cyber 3, then you will need to disable _Real-time Protection_ again as Windows automatically turns it on.

Lastly, make sure you create a snapshot before you execute Jigsaw so that you can revert to it.

<!--kg-card-end: markdown--><!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->
# Running Jigsaw

To start with our analysis, let's run _Jigsaw_ by itself by double-clicking it. You may get a couple of prompts. Just click _Run_ or _OK_ on each of them.

At this point, nothing will seem to be happening. After a minute or two (You shouldn't have to wait more than 3 minutes). You'll see a screen like the one below.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-24-at-11.55.55-PM.png" class="kg-image"><figcaption>Jigsaw message of terror</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

You can view the _Encrypted Files_ by clicking _View encrypted files_ on the _Jigsaw_ window. Play around with it for a few minutes and then reset the VM.  
_Snapshots -\> Restore Snapshot_. Click _Don't Save_ when prompted.

<!--kg-card-end: markdown--><!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->

Now that we've seen _Jigsaw_ in action, let's use some of the tools that we downloaded in Learning Cyber 3 to analyze the malware.

First, open Process Hacker by double clicking ProcessHacker.exe. You'll see an output similar to below.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-25-at-12.17.43-AM.png" class="kg-image"><figcaption>ProcessHacker</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->
## Process Hacker

Think of _Process Hacker_ as a Task Manager on steroids that offers lots of other features. We'll go over a few of these features below:

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

**Parent-Child Relationships**  
_Process Hacker_ presents the relationship between processes and their parents or children. Often, a process will create another process. Consider _System_ and _smss.exe_ above. In this case, _System_ is the parent and _smss.exe_ is the child. This is important for detecting anomalies. For example, it's anomalous for Microsoft Word to create a child process for _cmd.exe_. This is a common technique that malware utilizes.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

**Process Information**  
The highlighting of each process represents different types of processes. You can view the rules for highlighting by clicking  
_Hacker -\> Options_ and selecting the _Highlighting_ tab at the top.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-25-at-12.28.20-AM.png" class="kg-image"><figcaption>ProcessHacker highlighting rules</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

**Strings**  
Like PEStudio, _ProcessHacker_ has a _Strings_ function.

<!--kg-card-end: markdown--><!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->
# Evaluating Jigsaw with ProcessHacker
<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Minimize the process trees for each of the processes by clicking the small arrow next to some of the processes. The will hide the child processes. Maximize the process tree for _explorer.exe_ so we can see what happens when we run _Jigsaw_.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-25-at-12.49.10-AM.png" class="kg-image"><figcaption>ProcessHacker with most of the child processes hidden</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

With _ProcessHacker_ open, double-click _Jigsaw.exe_.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-25-at-12.50.33-AM.png" class="kg-image"><figcaption>ProcessHacker initial Jigsaw execution</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Notice that _jigsaw.exe_ has appeared a child process of _explorer.exe_. Interestingly, the Description says _Firefox_. Click run and let's see what happens.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-25-at-12.53.30-AM.png" class="kg-image"><figcaption>ProcessHacker Jigsaw running</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Now that _Jigsaw_ is running, it seems to have changed its name to _drpbx.exe_. Remember that we saw _drpbx.exe_ in _PEStudio_ using its strings functionality.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->
## ProcessHacker Strings

Let's examine _Jigsaw_'s strings while its running in memory. You can view the strings for a process's memory by right-clicking the process in _ProcessHacker_ and then selecting _Properties_. The properties window will appear. You can view the strings by clicking the _Memory_ tab and then clicking the _Strings_ button in the top right. Click _OK_ when the prompt appears.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Let's see if we can find the string for _drpbx.exe_. You can try scrolling through or because we know what we are looking for:

- Click filter
- Select _contains_
- Type _drpbx.exe_ in the prompt and click _OK_
<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-25-at-1.00.57-AM.png" class="kg-image"><figcaption>ProcessHacker drpbx.exe strings</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Revert the windows VM to pre-execution state again. Let's examine _Jigsaw_ using _Procmon_.

<!--kg-card-end: markdown--><!--kg-card-begin: hr-->
* * *
<!--kg-card-end: hr--><!--kg-card-begin: markdown-->
# Using Procmon

Open Procmon by double-clicking _Procmon.exe_.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-25-at-1.13.02-AM.png" class="kg-image"><figcaption>Procmon</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Procmon can be extremely busy, so its important to filter and control its output. You can turn Procmon's capture functionality on and off by clicking the _Magnifying Glass_ icon on the top of the screen. Go ahead and turn it off now.

You can clear the existing output by clicking the button that looks like a page with an eraser on it (mouse over the icons if you are having trouble). Go ahead and clear the results.

Now that we've cleared out the existing output, turn the capture back on and run Jigsaw.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Next, let's filter the results by clicking the icon below.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-28-at-6.50.41-PM.png" class="kg-image"><figcaption>Procmon Filter Button</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Once you click the filter button, you'll be presented with the screen below.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card"><img src="/content/images/2019/03/Screen-Shot-2019-03-28-at-6.52.47-PM.png" class="kg-image"></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Click the drop-down that says _Architecture_ and change it to _Process Name_. Next click the empty drop-down, select _drpbx.exe_, and click _Add_.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-28-at-7.03.33-PM.png" class="kg-image"><figcaption>Procmon drpbx.exe Filter</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Click _Apply_ and then _OK_. You'll notice that the output has been filtered to only include _drpbx.exe_ operations.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-28-at-7.04.00-PM.png" class="kg-image"><figcaption>Procmon Filtered Output</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Obviously, there's still a ton of output. Let's look at some of the operations.

**Process Operations**

- Process Start
- Thread Create
- Load Image

**File System Operations**

- CreateFile
- QueryBasicInfo
- CloseFile
- CreateFileMap

**Registry Operations**

- RegOpenKey
- RegQueryValue
- RegCloseKey
<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

Procmon can filter based on operations as well. Let's look at the files that Jigsaw has written. Click the filter button again and filter by _Operation is WriteFile_. Click _Add, Apply,_ then _OK_.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-28-at-7.18.35-PM.png" class="kg-image"><figcaption>Procmon Operation WriteFile Filter</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Look through the output.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-28-at-7.18.54-PM.png" class="kg-image"><figcaption>Procmon WriteFile Operations</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Notice all of the entries that end in _.fun_. Let's examine the first entry in explorer.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-28-at-7.22.45-PM.png" class="kg-image"><figcaption>Explorer background.jpg.fun</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Notice that only one file has the suspicious _.fun_ extension. Examination reveals that the _.fun_ extension represents files that Jigsaw has encrypted. Go back to Procmon and scroll through the output.

<!--kg-card-end: markdown--><!--kg-card-begin: image--><figure class="kg-card kg-image-card kg-card-hascaption"><img src="/content/images/2019/03/Screen-Shot-2019-03-28-at-7.27.07-PM.png" class="kg-image"><figcaption>Procmon Operation WriteFile Filter cont..</figcaption></figure><!--kg-card-end: image--><!--kg-card-begin: markdown-->

Scrolling through the output, we can see that Jigsaw appears to only encrypt certain file types.

**Observed Encrypted File Types:**

- .jpg
- .zip
- .png
- .txt
- .gif
- .xml
- .db
- .dat
- .svg

**Observed Skipped File Types**

- .exe
- .cfg
- .ps1

We haven't looked over everything, but at this point we've observed that Jigsaw exercises some level of discretion when encrypting files.

<!--kg-card-end: markdown--><!--kg-card-begin: markdown-->

We've only begun to scratch the service of the information that Procmon collects. Take a few minutes and look around at some of the other operations before you move on. In the next post we'll continue with dynamic malware analysis by examining network connections.

<!--kg-card-end: markdown-->