---
layout: post
title: Understanding Protected Management Frames - Part 2
date: 2024-06-28
author: TheXero
comments: true
categories: wifi
description: Learn about the limitations of Protected Management Frames (PMF) in Wi-Fi security, including gaps that can be exploited through passive listening, evil twin APs, and Wi-Fi jamming.
excerpt: Learn about the limitations of Protected Management Frames (PMF) in Wi-Fi security, including gaps that can be exploited through passive listening, evil twin APs, and Wi-Fi jamming.
tags: [wifi, training, PMF, Wi-Fi security, vulnerabilities]
thumbnail: /images/security-breach.jpg
image: /images/security-breach.jpg
---

Before diving into the main content, let's quickly recap the essentials of Protected Management Frames (PMF).

### What are Protected Management Frames (PMF)?
Protected Management Frames (PMF) provide enhanced protection for various management frames in Wi-Fi networks, which are critical for the proper functioning of Wi-Fi communications. PMF primarily secures the following types of management frames:

- **Association**
- **Authentication**
- **De-authentication**
- **Disassociation**
- **Re-association**

### Limitations of PMF
Despite its protections, PMF does not cover all aspects of Wi-Fi network security. For instance, PMF is not applicable to Open or WEP-protected networks. Additionally, it only protects states one through three of the state machine. An exploitable vulnerability lies in the handshake process for WPA-Personal (WPA-PSK), which occurs outside of PMF’s protection, in state four.

### Understanding the Wi-Fi State Machine
To better understand the scope of PMF, let's look at the Wi-Fi state machine:

![state_machine](/images/state_machine.png)

In the context of PMF, protection only applies to states one through three, as well as the De-authentication and Disassociation frames. With PMF enabled, it becomes impossible to inject frames and interfere directly with the state machine during these states. This restricted scope of protection raises important considerations.

### How to Bypass PMF Protections
Several strategies can bypass or circumvent PMF protections, ranging from simple to more complex approaches. Let's discuss a few notable options.

#### 1. Sit and Listen
The most straightforward method involves passive listening. To capture the Pre-Shared Key (PSK) handshake, all we need to do is listen for it. Each time a client attempts to rejoin the target network, the handshake is transmitted. This method is passive and non-intrusive, making it an effective starting point.

#### 2. Evil Twin Access Points
Many devices continuously send out Probe Requests even when they are already associated with a network. By setting up an evil twin Access Point that mimics the target network, we can potentially trick a client into attempting to associate with our rogue AP. Although the association will fail, it will cause the client to disassociate from the legitimate network. Once disassociated, the client will attempt to reconnect to the real network, allowing us to capture the handshake during this process.

#### 3. Wi-Fi Jamming
Another approach involves actively disrupting Wi-Fi communications by creating as much radio frequency interference as possible on the channels used by the Access Point (AP) and the client (STA). This technique, known as Wi-Fi jamming, aims to force a temporary disconnection between the AP and the STA. By jamming the communication, the devices may experience a forced timeout, after which they will attempt to reconnect. During this reconnection attempt, we can cease the jamming and listen for the handshake. This method requires more aggressive tactics but can be effective in forcing the desired behavior.

### Conclusion
While PMF significantly enhances the security of Wi-Fi management frames, it is not a perfect solution. Its limitations, particularly concerning the protection of the WPA-Personal handshake, leave room for various bypass strategies. By understanding these shortcomings and the methods to exploit them, we can better appreciate the complexities and ongoing challenges in securing Wi-Fi networks.
