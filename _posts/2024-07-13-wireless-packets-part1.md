---
layout: post  
title: "Wireless Packets - Part 1: The MAC Frame"  
date: 2024-07-13
author: TheXero  
comments: true  
categories: blog  
description: Explore the 802.11 MAC frame structure in detail, including its header, data, and frame check sequence. Enhance your understanding of wireless packets and how they function within Wi-Fi networks. 
excerpt: Delve into the 802.11 MAC frame header, data, and frame check sequence to gain a comprehensive understanding of wireless packet structures and their role in Wi-Fi communication. 
tags: [training, wireless, MAC frame, 802.11, PMF]  
thumbnail: /images/mac-frame.png  
image: /images/mac-frame.png
---

![802.11 MAC Frame](/images/mac-frame.png)

The 802.11 MAC frame serves as the cornerstone of wireless communication, encompassing essential components that facilitate efficient data transmission and robust network management.

### Frame Header

At a maximum length of 36 bytes, the MAC frame header consists of several critical fields:

#### Frame Control Field

The **Frame Control** field, occupying 2 bytes (16 bits), meticulously defines the frame's type, subtype, and operational parameters:

- **Protocol Version**: A 2-bit field specifying the current protocol version, typically set to 0.
- **Type**: A 2-bit field determining the frame’s primary function:
    - **Management (0x00)**: Handles network management tasks such as association, authentication, and beaconing.
    - **Control (0x01)**: Manages access to the wireless medium with functions like RTS (Request to Send), CTS (Clear to Send), and ACK (Acknowledgement).
    - **Data (0x10)**: Transports user data across the network, encapsulating higher-layer Protocol Data Units (PDUs).
    - **Extension (0x11)**: Reserved for specialised purposes, rarely used in standard operations.
- **Subtype**: A 4-bit field defining specific functions within each frame type, e.g., 0x0000 for an association frame and 0x0101 for a probe response frame.
- **To DS and From DS**: Each 1-bit field indicates whether the frame is destined to or from the distribution system, crucial for address interpretation and routing.
- **More Frag**: A 1-bit flag signalling if more fragments of the frame will follow.
- **Retry**: A 1-bit flag indicating if this frame is a retransmission due to unacknowledged previous transmissions.
- **Power Mgmt**: A 1-bit flag indicating the power-saving state of the sender: 0 for active mode, 1 for power-save mode.
- **More Data**: A 1-bit flag indicating if additional data is buffered for a client.
- **Protected Frame**: A 1-bit flag indicating the use of encryption or authentication within the frame. This field becomes especially pertinent when discussing Protected Management Frames (PMF), which enhance the security of management frames by providing protection against forgery and eavesdropping.
- **Order**: A 1-bit flag indicating if the frame uses the Strictly-Ordered service class, rarely implemented and potentially omitted in future standards.

A thorough understanding of the **Frame Control** field is essential for mastering Wi-Fi security and optimising network performance.

#### Duration / ID

The **Duration/ID** field, spanning 2 bytes, serves dual purposes depending on the frame type and subtype:

- **Association ID (AID)**: In Power-Save Poll frames (Type 1, Subtype 10), the 14 least significant bits of the Duration/ID field store the AID, uniquely identifying associated clients.
- **Duration**: In other frames, this field denotes the duration in microseconds for which the medium is reserved, facilitating network coordination and collision avoidance.

#### Addresses

Each 6-byte (48-bit) address in the MAC header fulfils specific roles based on the To DS (To Distribution System) and From DS (From Distribution System) fields:

![Addresses](/images/addresses.png)

- **DA (Destination Address)**: Identifies the intended recipient of the frame.
- **RA (Recipient Address)**: Typically used in mesh networks to specify the immediate recipient.
- **SA (Source Address)**: Identifies the sender of the frame.
- **TA (Transmitter Address)**: Indicates the device physically transmitting the frame.

These addresses are pivotal in determining the frame's path through the network.

#### Sequence Control

This subheader includes:

- **Sequence Number**: A 12-bit field to prevent frame duplication during retransmission.
- **Fragment Number**: A 4-bit field indicating the fragment's position within a larger frame.

#### Data

This variable-length field supports payloads up to 2324 bytes, depending on the encryption method used:

- **WEP (Wired Equivalent Privacy)**: Supports data sizes ranging from 8 to 2312 bytes.
- **TKIP (Temporal Key Integrity Protocol)**: Allows data sizes ranging from 20 to 2324 bytes.
- **CCMP (Counter Mode with Cipher Block Chaining Message Authentication Code Protocol)**: Accommodates data sizes from 16 to 2320 bytes.

#### Frame Check Sequence

This field contains the frame’s CRC (Cyclic Redundancy Check) value, ensuring data integrity during transmission.

### Frame Types Explained

#### Management Frames

**Management frames** play a critical role in network establishment and maintenance:

- **Beacon Frames**: Broadcast by Access Points (APs) to announce their presence and provide synchronisation and timing information to clients.
- **Probe Request/Response Frames**: Clients initiate probe requests to discover available APs, and APs respond with probe responses containing network information.
- **Authentication Frames**: Used to authenticate a station (client) with an AP, ensuring that only authorised devices gain network access.
- **Association Request/Response Frames**: A client sends an association request to join an AP after authentication, and the AP responds with an association response to accept or deny the request.
- **Reassociation Request/Response Frames**: Used when a client roams from one AP to another within the same Extended Service Set (ESS).
- **Deauthentication Frames**: Sent by an AP to terminate a client's authenticated session, prompting the client to re-authenticate to continue communication.
- **Disassociation Frames**: Sent by a client to inform an AP of its desire to disconnect from the network.

With the advent of [Protected Management Frames](Understanding-PMF) [(PMF)](Understanding-PMF), management frames have become more secure. PMF enhances the security of management frames, protecting them from forgery and eavesdropping attacks. This feature is crucial for maintaining the integrity and confidentiality of network operations, especially in enterprise environments where the risk of malicious activity is higher.

Understanding these frame types is crucial for managing and securing wireless networks effectively, as each frame serves a distinct role in network operations and data transmission.

---

### Further Reading and Resources

For those interested in diving deeper into the technical intricacies of the 802.11 MAC frame and exploring advanced Wi-Fi security measures, consider these resources:

- [IEEE 802.11 Standards](https://www.ieee.org/)
- [Wi-Fi Alliance](https://www.wi-fi.org/)

Stay tuned for Part 2 of this series, where we will explore the nuances of data frames and control frames in greater detail.
