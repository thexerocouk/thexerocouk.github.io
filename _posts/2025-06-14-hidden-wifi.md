---
layout: post
title: "Hacking Hidden WiFi Networks"
date: 2025-06-14
author: TheXero
comments: true
categories: wifi
tags: [wifi, hidden-ssid, scapy, wireless-hacking, beacon-frame, hashcat, psk-cracking, pentesting]
description: "Explore the truth behind hidden SSIDs and learn how attackers can reveal and exploit non-broadcasting WiFi networks using beacon spoofing and Scapy."
keywords: hidden SSID, non-broadcasting WiFi, beacon spoofing, WPA2 cracking, wireless security, Scapy, hashcat, hcxpcapngtool
excerpt: "Think hidden SSIDs make your WiFi more secure? Think again. Discover how wireless attackers reveal, spoof, and crack non-broadcasting networks using Python, Scapy, and Hashcat."
image: /images/ssid_hidden.webp
thumbnail: /images/ssid_hidden.webp
---

## TL;DR

Hidden SSIDs don’t provide real security—they only hide your network name from the casual observer. In this post, we explore how attackers can reveal a hidden SSID by monitoring probe responses and forge beacon frames using Scapy to crack WPA2 passwords using tools like `hcxpcapngtool` and Hashcat. If you’re relying on “security by obscurity,” it’s time to rethink your wireless defenses.

---

I have received alot of queries recently, around hidden SSIDs, specifically if there is any security implications of it. After all, if no one can see my network, then no body can possibly target it.

Now, lets clear up a few bits first, a hidden SSID is not a recommended security practice. A hidden SSID is actually known as a non-broadcasting SSID, and provides a false sense of security. Lets explore why this is the case.

A standard WiFi network presents itself by sending out what is known as a Beacon frame.  This beacon frame is used to actively broadcast itself as being available to connect to. A hidden, or non-broadcasting SSID on the other hand, still sends the same beacon frames, but with an empty SSID field.

![Hidden SSID](/images/ssid_hidden.webp)

Now, the standard device might have trouble connecting to a hidden network for one of two reasons.
1. The modern day default, is that STA devices can be configured to search for networks, but typically do not reveal what network they are searching for. Instead, they rely on the response frame containing a known network.
2. Some older devices, cannot be configured to connect to a hidden network.

What ends up happening, is the device will send a probe request with a missing SSID, and the hidden network, will just ignore it. It will not respond to the request.

![Hidden Probe](/images/probe_hidden.webp)


To get around, this STA must be configured to ask for the specific network it is looking for, only then will the AP send a response containing the SSID.

![Probe with SSID](/images/probe_ssid.webp)


After receiving this valid response frame, the device then continues through the WiFi states and exchanges the authentication frames, and following with the association frames, again while having to disclose the SSID it is attempting to associated with.

![Association with SSID](/images/association_ssid.webp)


And so, despite some might believe using a hidden or non-broadcasting SSID is a security feature, really not. It is more security by obscurity and hassle than anything else, as the world is more security and privacy conscience than ever before, the non-broadcasting network name is still leaked during various stages.

But I hear you say, "what about during the 4-way handshake, the SSID is the salt. Without it, you cannot crack the password." To that, I would say, you are 100% correct. The way the PSK is generated is with both the password as well as the SSID. Lets break this down demonstrate this in human readable python code.


```
# Imports the required PBKDF2 module
from pbkdf2 import PBKDF2

# Defines the ESSID (used as the salt)
ESSID=WPA2

# Defines the password used
PASSWORD=password

# Generates the PMK 
PMK=PBKDF2(PASSWORD, ESSID, 4096).read(32).hex()

# Outputs the PMK hash to the screen
print(PMK)
```

More information on this exact process can be found in the RFC over at https://www.rfc-editor.org/rfc/rfc8018.txt 

So yes, in order to crack the password, you need the true SSID for network. 

Now, imagine this scenario, you are on a wireless pentest, you got the 4-way handshake, all is good, and you export the files. You go back to your office to put the hash onto your custom built escobar password cracking device. 

And it hits you, that dreaded error from hcxpcapngtool. Information: `no hashes written to hash files`. When you exported the selected frames, forgetting that the SSID is not broadcasted. You exported the full 4-way handshake with the standard beacon frame that did not contain the SSID. How can you crack the password now?

But you know the SSID. Beacon frames are not verified, so, lets forge one ourselves with Scapy, so we can crack this hash out.

You create the following python code:
```
#!/usr/bin/python3

# Import all parts to send a forged Beacon frame
from scapy.all import Dot11Beacon, Dot11Elt, Dot11, RadioTap, sendp

# Define the known SSID
SSID="WPA2"

# Create the broadcast Beacon frame
dot11 = Dot11(
        type=0,
        subtype=8, 
        addr1='ff:ff:ff:ff:ff:ff', 
        addr2='50:d4:f7:ff:ef:d8', 
        addr3='50:d4:f7:ff:ef:d8')
Beacon = Dot11Beacon()

# Set the SSID field
ESSID = Dot11Elt(ID='SSID', info=SSID, len=len(SSID))

# Construct the frame
frame = RadioTap()/dot11/Beacon/ESSID

# Send 10 forged frames
sendp(frame, iface='wlan0', count=10)
print("Sent Beacon.")
```

With Wireshark open listening capture the forged Beacon frame as shown below:
![Forged Beacon Frame](/images/forged_beacon.webp)

Now all you need to do in merge the forged beacon frame into the existing pcapng file and convert the hash to hashcat using hcxpcapngtool. And finally, its now time to crack the hash using hashcat
```# hashcat -m 22000 forged.22000 /usr/share/wordlists/rockyou.txt --quiet
8761551efb7b088761cdcf31de358919:50d4f7ffefd8:0025d38b6d09:WPA2:princess
```

I hope you all enjoyed this post. Let me know what you would like to see next.

Toby



### Ready to Level Up Your Wireless Hacking Skills?

If you found this post valuable and want to dive deeper into real-world WiFi attack techniques, rogue AP setups, handshake harvesting, and advanced cracking, check out my **WiFi Mastery** course.

Enrollment is open now at [https://training.thexero.co.uk/p/wifi-specialist](https://training.thexero.co.uk/p/wifi-specialist) and take your wireless skills to the next level.
