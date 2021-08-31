---
layout:     post
title:      "LLMNR/mDNS/NBNS Spoofing, pt. 2"
date:       2018-07-31
tags:       [responder, inveigh, smb, llmnr]
---
## Continued
I wanted to perform the same attack using [Inveigh][Inveigh repo] coming from a Windows system.  See the [previous post](/2018-07-30-smb-spoofing-pt-1/) for a bit of backstory and details on the setup.
  
In this scenario we're using the Server 2012 box (`10.0.0.107`) as the SMB server (where we'll deploy Inveigh) and a Windows 10 system (`10.0.0.108`) as the client.
  
### Setting up Inveigh
First grab the latest Inveigh release (1.3.1 at the time of this writing) by going to the [Inveigh repo][Inveigh repo], clicking the releases tab, and downloading the source.
  
![Inveigh download link](/assets/img/download-inveigh-release.jpg)
  
Extract the source, open an administrative Powershell session, and navigate to the folder you extracted the source to.  Also change the Powershell trusted execution policy as per the screenshot.
  
![Inveigh extracted and powershell window](/assets/img/inveigh-extracted.jpg)

Next import the Inveigh Powershell module so we can use it as in the examples.  Run the command below.
`Import-Module .\Inveigh.psm1`
  
Respond to the prompts asking if you really want to run the scripts.  I had to acknowledge three such prompts.

![Import inveigh](/assets/img/confirm-inveigh-import.jpg)
  
And that's pretty much it.  Now we can run Inveigh with the command below (as per the example in the readme) and see the pretty output.
`Invoke-Inveigh -ConsoleOutput Y -NBNS Y -mDNS Y -HTTPS Y -Proxy Y`

![Invoke-Inveigh](/assets/img/invoke-inveigh.jpg)

### Grab the hash
Ok, now head over to our client (the Win 10 VM) and attempt to map the non-existent share and see how Inveigh responds.

![Net use fail](/assets/img/net-use-fail.jpg)

Well that's no good.  I'm gonna guess the firewall is in the way.  Let's disable it like a good sysadmin, and try again.

![Net use nofail](/assets/img/net-use-nofail.jpg)

This looks at least a little more promising, yes?  Let's see what Inveigh has to say.

![Inveigh captured hash](/assets/img/inveigh-captured-hash.jpg)

Much better, thank you ~~Aziz~~ Windows.

As we can see, Inveigh displays two captured hashes.  One as the client attempts the SMB mount, and another as Windows attempts an HTTP request for the share.  The hashes do differ, but copying/pasting these into a file and then throwing it into john nets us the password once more.

![John cracking Inveigh hashes](/assets/img/john-crack-inveigh-hashes.jpg)

Interestingly it's only able to crack one of the hashes.  I'm not sure why here, but perhaps we'll explore that more in part 3.
  
### Conclusion
So there wasn't much more to this example than there was to the Responder example, and thus concludes a very basic look into the usage of Responder and Inveigh.  In part 3 of this series I'll delve a little deeper into some of the advanced features of both tools.  This is the first I've played with them so inevitably I'll get some things wrong along the way, but that's how we learn.  Thanks for reading!

[Inveigh repo]: https://github.com/Kevin-Robertson/Inveigh
