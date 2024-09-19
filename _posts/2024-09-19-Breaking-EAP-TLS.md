---
layout: post
title: "EAP-TLS: Breaking Into Secure TLS Deployments"
date: 2024-09-19
author: TheXero
comments: true
categories: wifi
description: "Discover how attackers can exploit vulnerabilities in EAP-TLS, one of the most secure Wi-Fi authentication protocols, and learn how to protect your network against these threats."
excerpt: "Explore the hidden vulnerabilities of EAP-TLS and learn how attackers break into even the most secure TLS deployments. A must-read for network security professionals."
tags: [training, wireless, security, EAP-TLS, penetration testing, Wi-Fi]
thumbnail: /images/eap-tls.png 
image: /images/eap-tls.png
---

In our last post, we discussed various authentication methods for enterprise Wi-Fi networks. Today, we’ll be focusing on **EAP-TLS (Extensible Authentication Protocol - Transport Layer Security)**, regarded as one of the most secure authentication protocols in use today. We’ll walk through its process, highlight potential vulnerabilities, and examine what attackers can exploit. But first, a quick refresher.

### A Quick Recap

EAP-TLS provides robust security by enforcing **mutual authentication**—both the client and the server must validate each other’s digital certificates. This mutual validation ensures a high level of trust between the connecting device and the network.

![The EAP-TLS Process](/images/eap-tls.png)

### The EAP-TLS Process: Step-by-Step Breakdown

1. **EAPoL Start**  
    The process begins with the **EAPoL Start** messages exchanged between the **Supplicant (STA)** and the **Access Point (AP)**. This step kicks off the authentication and key management process, allowing the transfer of essential data to the RADIUS server.
    
2. **Requesting Authentication**  
    After the EAPoL packets are exchanged, the AP requests the STA’s identity, often revealing a potential username. The STA replies with this identity, which is forwarded to the backend RADIUS server as part of an access request. Up to this stage, the process remains anonymous, as no actual authentication has taken place.
    
3. **Mutual Authentication**  
    The **RADIUS server** sends its **TLS certificate** to the STA. If the STA verifies the certificate, confirming the legitimacy of the network, it responds by sending its **client certificate**. The RADIUS server then validates the client certificate. If both validations succeed, authentication is complete.
    
4. **Access Granted**  
    The AP relays an **EAP Success** message to the STA, signifying that access has been granted.
    
5. **4-Way Handshake**  
    Once access is authorised, the STA and AP initiate the **4-Way Handshake**, establishing an encrypted channel for secure communication on the network.

Now that we’ve covered the basics, let’s dive into some of the vulnerabilities within EAP-TLS.

### Potential Vulnerabilities

#### **Username Disclosure**

During the authentication request stage, the STA discloses an identity—usually the device’s hostname or the user’s username. While this information isn’t part of the mutual authentication process, it can provide useful reconnaissance data for attackers.

**Remediation**: Implement **EAP Identity Privacy**, which allows the use of a pseudonym during the initial authentication stages, keeping the true identity hidden from potential attackers.

#### **Certificate Trust Issues**

The security of EAP-TLS is heavily dependent on the proper validation of certificates. However, improper validation can lead to vulnerabilities. Let’s break down two common issues.

##### **Self-Signed Certificates**

A **self-signed certificate** is issued and validated by the same entity, making it inherently insecure since no trusted third-party verification is involved. These certificates should never be used in EAP-TLS setups.

##### **Third-Party Issued Certificates**

While widely used, third-party certificates (such as those for HTTPS) only verify the certificate issuer, not the network itself. This means that while the certificate is trusted, it doesn’t guarantee that it’s tied to your specific network.

**Remediation**: The best practice for EAP-TLS is to use an **internal Certificate Authority (CA)**. This allows devices to trust only certificates issued by your internal CA, preventing external CAs from issuing valid certificates for your network, thereby mitigating external certificate-based attacks.

### Real-World Exploitation Scenarios

While the EAP-TLS protocol is secure, attackers often target **client devices (STAs)** instead of the network infrastructure itself. Misconfigured clients or weak certificate validation practices can expose vulnerabilities.

In many cases, the most successful attacks focus on devices rather than the network. **Misconfigured devices** that fail to validate certificates properly or allow for weak identity privacy measures present easier targets than a well-implemented EAP-TLS network.

#### Alternative Exploitation Scenarios: Rogue Networks

Consider this: many corporate devices, while configured securely for EAP-TLS, are mobile. Users take them home or travel with them. These devices frequently send out **Probe Requests** for previously known networks. What happens if the device sees a familiar home network, say **NETGEAR**? It will likely attempt to connect to this trusted network without user intervention.

If an attacker sets up a **rogue or evil-twin network** mimicking a trusted SSID, the device could automatically connect, giving the attacker a foothold. Once connected, the attacker can execute further attacks, such as credential theft or man-in-the-middle (MitM) exploitation, all without user interaction.

### Conclusion

While EAP-TLS remains one of the most secure wireless authentication methods available, vulnerabilities often stem from poor configuration—particularly on client devices. As attackers, it’s often more effective to focus on exploiting weak configurations in devices rather than attempting to break the network infrastructure itself.

For more in-depth discussions and updates, follow our blog and join the conversation on our [Discord](https://discord.gg/YEfgvuqyDn). Have any questions or topics you’d like us to cover? Feel free to leave a comment below.
