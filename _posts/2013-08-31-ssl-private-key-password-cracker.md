---
layout: post
title:  SSL Private Key Password Cracker
date:   2013-08-31
author: TheXero
comments: true
categories: tools
description: HTTP servers typically are used to host web applications, but may also be hosting the same application over secured HTTPS. During a recent engagement, an undisclosed directory was found and contained a password protection SSL certificate.
excerpt: Following a recent pentest I performed for a client I stumbled upon their private SSL certificate. The SSL key was password encrypted, thus could not be used directly without knowing...
tags: [ssh, ssh, key, pasasword, cracker]

---

Following a recent pentest I performed for a client I stumbled upon their private SSL certificate. The SSL key was password encrypted, thus could not be used directly without knowing the password to decrypt the key.

A small shell script was created in an attempt at discovering a dictionary passphrase use to encrypt the key. The shell script can be at the following URL: <a href="https://raw.githubusercontent.com/nullsecuritynet/tools/master/cracker/ssl-crack/release/ssl-crack.sh" target="_blank">https://raw.githubusercontent.com/nullsecuritynet/tools/master/cracker/ssl-crack/release/ssl-crack.sh</a>
