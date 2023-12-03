---
layout: post
title:  nullsploit engine
date:   2019-05-03
author: TheXero
comments: true
categories: tools
description: A work-inprogress exploitation engine released to help demonstrate quickly demonstrate the seriousness of unpatched vulnerable software. Only a small number of exploits exist, however these should be reliable across multple windows versions and configurations.
excerpt: The nullsploit engine is a work in progress exploitation framework. Currently only a limited number of exploits are available, but these should be stable across multiple Windows installations. Features...
tags: [exploit, tool, python, exploitation]
thumbnail: /images/tools-nullsploit-200x200.png
image: /images/tools-nullsploit.png
---

The nullsploit engine is a work in progress exploitation framework. Currently only a limited number of exploits are available, but these should be stable across multiple Windows installations.

Features currently implemented include payload choices as well as options and a completely custom encoder and payload handler. At present the available payloads include the WinExec API and a bind shell.

A short demo of the WorldMail IMAP exploit module is shown below.

<asciinema-player src="/resources/244065.cast" cols="115" rows="27" theme="monokai"></asciinema-player>
<br />

The project is available on GitHub at the following link: <a href="https://github.com/TheNullSecXero/nullsploit" target="_blank">https://github.com/TheNullSecXero/nullsploit</a>