---
layout: post
title:  Understanding Protected Management Frames
date:	2024-06-23
author: TheXero
comments: true
categories: blog
description: Discover the importance of Protected Management Frames (PMF) in Wi-Fi networks. Learn how PMF secures management frames, preventing tampering and unauthorised injections. Explore the different configuration states of PMF and understand its role in enhancing Wi-Fi security. Stay tuned for our next post on how attackers defeat PMF and how to protect your network against advanced threats.
excerpt: Discover the importance of Protected Management Frames (PMF) in Wi-Fi networks. Learn how PMF secures management frames, preventing tampering and unauthorised injections. Explore the different configuration states of PMF and understand its role in enhancing Wi-Fi security. Stay tuned for our next post on how attackers defeat PMF and how to protect your network against advanced threats.
tags: [pmf,wpa,wifi,wireless,training]
thumbnail: ...
image: ...
---

### Understanding Protected Management Frames (PMF) in Wi-Fi
Before delving into Protected Management Frames (PMF), it's essential to understand what management frames are and their role in Wi-Fi communication.

## What Are Management Frames?
In Wi-Fi, management frames are crucial for establishing and maintaining wireless communication. They facilitate various operations such as network discovery, authentication, association, and disassociation. There are many types of management frames, but we will focus on the following ten:
- **Association Request**
- **Association Response**
- **Authentication**
- **Beacon**
- **De-authentication**
- **Disassociation**
- **Probe Request**
- **Probe Response**
- **Re-association Request**
- **Re-association Response**

### Wi-Fi State Machine
During the Wi-Fi state machine process, a device (STA) and an access point (AP) exchange multiple management frames. Here's a typical sequence:
1. **STA Probes for network**:
    - The STA sends a Probe Request frame to discover available networks.
2. **AP Responds**:
    - The AP replies with a Probe Response frame.
3. **STA Sends Authentication Frame**:
    - The STA sends an Authentication frame to initiate the authentication process.
4. **AP Responds with Authentication Frame**:
    - The AP responds with another Authentication frame.
5. **STA Sends Association Request**:
    - The STA sends an Association Request frame to join the network.
6. **AP Responds with Association Response**:
    - The AP replies with an Association Response frame, completing the association process.

In an open network configuration, once the STA associates with the AP, basic IP connectivity is established.

### The Vulnerability of Management Frames
Traditionally, management frames are transmitted in the clear, without encryption or authentication. This exposes them to potential attacks where malicious actors can inject fake frames into the network. A common attack involves injecting de-authentication frames, which can cause denial of service (DoS) by continuously disconnecting devices from the network.

## What Are Protected Management Frames (PMF)?
Protected Management Frames (PMF) enhance the security of management frames by protecting them from tampering and unauthorised replay attacks. PMF achieves this by adding a Message Integrity Check (MIC) information element to the frames sent from the AP.

### How PMF Works
1. **Initialisation**:
    - During the initial communication between the STA and AP, the AP includes the PMF capability in its frames.
2. **Encryption**:
    - Once PMF is enabled, the STA encrypts any subsequent management frames exchanged with the AP.
3. **Integrity Check**:
    - The MIC ensures that any management frame received has not been altered or injected by a third party. Frames that fail the integrity check are discarded before processing.

With PMF, management frames are encrypted using the 802.11i (WPA2) standard, and any unauthorised frames are filtered out, enhancing the overall security of the Wi-Fi network.

## PMF Configuration States
PMF can be configured in three states:
1. **Disabled**:
    - PMF is not used, and management frames are sent unprotected.
2. **Compatible**:
    - PMF is optional. STAs that support PMF can enable it, but it is not mandatory. This allows devices that do not support PMF to still join the network.
3. **Required**:
    - PMF is mandatory. Only STAs that support and enable PMF can join the network. Devices that do not support PMF are denied access.

### Conclusion
PMF is a critical security feature that protects Wi-Fi management frames from tampering and unauthorised injection. By encrypting these frames and ensuring their integrity, PMF helps prevent attacks that can disrupt network connectivity and compromise security. Configuring PMF appropriately based on network requirements enhances the overall security posture of Wi-Fi networks.

### To Be Continued...
Stay tuned for our next post, where we delve into the darker side of Wi-Fi security and explore the techniques used for defeating PMF. How do attackers circumvent these protections? Find out in our upcoming post!

