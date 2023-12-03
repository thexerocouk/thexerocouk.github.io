---
layout: post
title:  Abusing The Stack
date:   2011-10-26
author: TheXero
comments: true
categories: exploit-development
description: Watch and replicate the entire process of discovering a new vulnerability, and the step-by-step process to develop custom exploit code from scratch to achieve full Remote Code Execution.  
excerpt: Abusing the Stack is a full tutorial, detailing the process of vulnerability discovery to developing custom exploit code to take advantage of a vulnerability. Once you have successfully been through...
tags: [exploit, development, hacking, stack, shellcode, ftp]
thumbnail: /images/abusing-the-stack-200x200.png
image: /images/abusing-the-stack.png
---

Abusing the Stack is a full tutorial, detailing the process of vulnerability discovery to developing custom exploit code to take advantage of a vulnerability.

<iframe src="https://www.youtube.com/embed/3VK4ixL4s2Y" allowfullscreen="allowfullscreen" width="975" height="600" frameborder="0"></iframe>

Once you have successfully been through the process of developing a custom exploit, you will soon realize that it’s a lot simpler and less of a black art than people think.

To start with, generally a Stack based Buffer Over Flow condition causes the target application to crash by overwriting the pointer to the next instruction, called EIP (Extended Instruction Pointer).

We first load up a simple python based fuzzer script and attempt to fuzz a free FTP server called FreeFloat FTP Server which is hosted on a machine in the lab with the IP of 192.168.72.129.

The program stops responding to our FTP requests after about 300 A’s after the command USER.

We then load the program into Immunity Debugger and attempt to replicate the crash once again and hopefully it will tell us a little bit more about the crash. In this case the target application crashed because EIP has been completely overwritten with 41414141 which is the hex equivalent to (4) letter As.

For simplicity sake we decide to export the target IP address and port to our local environment variables so that the potential of entering the wrong target address is minimized.

Then load up Metasploits tool Pattern Create and this creates us a unique string that we can use to replace the buffer to help identify the exact position before we get to the EIP overwrite, which turns out to be 230.

We then modify our buffer to include 230 As then send a DEADBEEF as the address to overwrite EIP and the rest of our buffer overflows into the ESP register which means that if we overwrite EIP to a memory address that has the assembly instruction of JMP ESP, then hopefully the we can jump to ESP and the next set of instructions.

We then send all hex bytes (minus the 00 as it would kill our TCP connection to the FTP server) and attempt to identify any bad characters that may be included in our shellcode later on.

Metasploit is then opened with the console interface and we begin to create test shellcode, while excluding the bad characters from the payload (identified previously) that will run the Windows calculator application. As the payload will be encoded we had to add 8 NOPs to our buffer so that there was sufficient room for the payload to decode itself.

Once the test shellcode is added we test our exploit which will hopefully crash the application once again but at the same time execute our code and open the Windows calculator, which ended up working as planned.

We go back to metasploit and create a Windows reverse shell payload, again excluding the bad characters found and write all the hex bytes to a file called shellcode which we then open with gedit.

We then replaced the test Windows calculator payload with the first stage of our newly created staged Windows reverse shell payload to complete our exploit.

Then we set up a metasploit to listen on port 4444 for a staged Windows reverse shell and executed our exploit, which resulted in the target machine connecting back to our machine. As we chose a staged payload our machine delivered stage 2 of the payload creating a full reverse Windows command prompt to be given to our machine and from then on we had full control over that session.
