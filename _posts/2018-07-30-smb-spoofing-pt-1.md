---
layout:     post
title:      "LLMNR/mDNS/NBNS Spoofing, pt. 1"
date:       2018-07-30
tags:       [responder, inveigh, smb, llmnr]
---
## Introduction
Recently I participated in a CTF against some Linux and Windows systems.  I scored maximum points but one of the systems had unsettled me and, after the fact, I had discovered I didn't use the intended route to gain access.  Now while `root` is `NT AUTHORITY\SYSTEM` is `root`, I clearly had lacked some knowledge that would have benefited me in the challenge.  What makes it worse is had I even bothered to open Wireshark at all during the CTF (literally, I hadn't opened it even once, wtf was I thinking), I would have seen a clue to my next steps.  My goal with these posts (there should just be a part 1 and 2) is to briefly describe the problem and then explore two tools for exploiting it ([TrustWave's Responder](https://github.com/SpiderLabs/Responder) in Python and [Kevin Robertson's Inveigh](https://github.com/Kevin-Robertson/Inveigh) in Powershell).  I may also include a part 3 to explore some of the other options of Responder as it appears at first glance to be capable of more than what I'm attempting here.
  
The box in question was reaching out to my attacking system and trying to map an SMB share while also passing username and password.  For those who aren't sure what to look for (myself included), such evidence appears as in the screenshow below.  
  
![Wireshark capture of SMB session attempt](/assets/img/wireshark-smb-session.jpg)
  
There's a bunch going on here, but at a high level we can see `10.0.0.107` (the client - Server 2012 R2) reaching out to `10.0.0.10` (the server - Kali), and the server consistently resetting the connection because, well, there's no service listening to field the request.  Further down the list we see NBNS NBSTAT queries coming from the client, as well as LLMNR A and AAAA queries.
  
So great, we've discovered a system attempting to map an SMB share that we aren't actively serving up.  How do we take advantage of this to see if there's anything to exploit?  The first attempt is using Kali so we'll be using Responder as it's made for Linux (and also works on macOS).
  
### Setting up Responder
First clone the git repo so we can play with it.  
`git clone https://github.com/SpiderLabs/Responder.git`
  
From the readme it says example usage is the following (changed for my environment).  
`maik@kali:~/Responder$ sudo ./Responder.py -I eth1 -wrf`

![Responder default run](/assets/img/responder-default-run.jpg)  
  
So that's neat.  Let's try and map the non-existent share again and see what it do.

![Client attmepting to map share](/assets/img/windows-map-share.jpg)  
  
The client gets a response and reports the share was mapped successfully (though Windows throws a "parameter incorrect" error when attempting to browse to it).  What does Responder report to us?

![Responder captured hash](/assets/img/responder-captured-hash.jpg)
  
Oooh we get an NTLMv2 hash reported back!  As per the Responder readme these hashes are also saved into the logs/ folder in a .txt file that is immediately consumable by john the ripper.  I know `Abcd123` exists in the rockyou wordlist, but not sure if it'll pick up on the `$` at the end.  Let's see!  
`maik@kali:~/Responder$ sudo john --wordlist=/usr/share/wordlists/rockyou.txt logs/SMB-NTLMv2-SSP-10.0.0.107.txt`
  
![John cracked hash](/assets/img/john-cracked-hash.jpg)
  
It does work.  That's super awesome!
  
### Conclusion
So we've shown a very basic example of how to use Responder to capture the NTLMv2 hash of a Windows (Server 2012 R2) system attempting to mount a share directly on our Linux system.  The odds of encountering this specific scenario in an engagement seem pretty far fetched, but the Responder docs do hint at being able to do some ARP poisoning and spoofing, so it will be interesting to see if we can redirect a mount request directed at one system back into our kali system (definitely part 3 material).  If not, Inveigh appears to be a Powershell equivalent, so we'll explore that in part 2.
