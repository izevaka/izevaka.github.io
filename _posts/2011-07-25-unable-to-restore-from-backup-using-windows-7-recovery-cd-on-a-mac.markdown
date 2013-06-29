---
layout: post
title: Unable to Restore From Backup Using Windows 7 Recovery CD on a Mac
tags:
- General
status: publish
type: post
published: true
meta:
  _jd_tweet_this: 'yes'
  _jd_twitter: ''
  _wp_jd_clig: ''
  _wp_jd_bitly: http://bit.ly/ptpV5c
  _wp_jd_wp: ''
  _wp_jd_yourls: ''
  _wp_jd_url: ''
  _wp_jd_target: ''
  _jd_wp_twitter: ! 'New post: Unable to Restore From Backup Using Windows 7 Recovery
    CD on a Mac - http://bit.ly/ptpV5c'
  _jd_post_meta_fixed: 'true'
  _edit_last: '1'
---
If you are restoring from backup made using Windows 7 Backup and Restore system image on Mac you might find that you can't access network location containing the backup. This is due to the network card's driver not being loaded. Don't worry though, there is a solution and once the driver is loaded the restore can proceed successfully.

Windows drivers for Mac come with Boot Camp installer, however they are two problems:  
 1. They are inside one of many installers.  
 2. You need to know which driver to use.

The first problem is pretty easy to solve - the installers are actually self-extracting archives, so they can extracted with WinRar. The second problem is a bit tougher to deal with and requires a bit of research, either on Windows and  MacOS to try and figure out which one of the hundreds of available drivers is the right one.

###Driver Details

So before jumping into recovery, and you should really do this in controlled circumstances - i.e. practice recovery, have a look at what network driver you have in Windows. Mine (15" April 2011 MacBook Pro) looks like this:  

<a href="http://www.somethingorothersoft.com/wp-content/uploads/2011/07/driver.png"><img src="http://www.somethingorothersoft.com/wp-content/uploads/2011/07/driver.png" alt="" title="driver" width="352" height="444" class="alignnone size-full wp-image-543" /></a>

If you are stuck and can only boot into MacOS, you look up the adapter's details in System Diagnostics for hints about what it's called.

###Extracting Boot Camp drivers
Get hold of MacOS install DVD or download those with Boot Camp Assistant in MacOS and put them onto a USB stick. Have a browse though the driver installers(<MacOS DVD>\Boot Camp\Drivers\) and extract any of the ones that might sound like they contain the network adapter that you need - Intel, Broadcom and NVidia are main suspects there. In my case it was BroadcomEthernet64.exe that contained the driver in question.

###Recovery
Boot into Windows Recovery CD and bring up the screen to load drivers. Navigate to the folder containing extracted driver installer and select the only .INF file there - b57nd60a.inf. You will be presented with a select driver dialog that, in the case of Broadcom, contains tons of duplicated drivers. Select second(first one didn't work) "Broadcom 570x Gigabit Integrated Controller".  

The add driver dialog doesn't really tell you whether or not installation was successful. You need to proceed to browse to the network location to actually find out if the driver worked. Side note: The system will not actually acquire an IP address through DHCP until you click to browse network. There is a short cut that you can take if a lot of trial and error is involved.  Bring up command prompt and type in "ipconfig". Once the driver is loaded, you will see an adapter in the command's output.

Once the network location is available, the restore process should proceed without problems. Leave all options default (including the scary "Format partition" checkbox) - it will not overwrite MacOS partition.

Happy restoring.
