# Networking Overview

**HTB Junior Cybersecurity Analyst — Introduction to Networking**
**Section 01 · Networking Overview**

---

<!--
  IMAGE SPOT: Cover / Banner image
  Suggested: A network topology illustration or HTB-style banner
  <img src="your-image-path.png" width="700">
-->

> **One-line summary:** Networking is not just about making devices talk to each other — it's about knowing *who* is talking to *whom*, *how*, and *whether that communication should exist at all.*

---

## Table of Contents

| # | Topic |
|---|-------|
| 1 | [What Is a Network?](#1-what-is-a-network) |
| 2 | [Network Components](#2-network-components) |
| 3 | [Why Networking Matters in Cybersecurity](#3-why-networking-matters-in-cybersecurity) |
| 4 | [Flat Networks](#4-flat-networks) |
| 5 | [Network Segmentation](#5-network-segmentation) |
| 6 | [Access Control Lists](#6-access-control-lists-acl) |
| 7 | [/24 and /25 Networks](#7-24-and-25-networks) |
| 8 | [The Pentester's Oversight](#8-the-pentesters-oversight) |
| 9 | [FQDN vs URL](#9-fqdn-vs-url) |
| 10 | [DNS](#10-dns) |
| 11 | [Routers](#11-routers) |
| 12 | [DMZ](#12-dmz) |
| 13 | [Secure Network Architecture](#13-secure-network-architecture) |
| 14 | [IP Phone Segmentation](#14-ip-phone-segmentation) |
| 15 | [Printer Security](#15-printer-security) |
| 16 | [Attack Perspective](#16-attack-perspective) |
| 17 | [Defense Perspective](#17-defense-perspective) |
| 18 | [Key Takeaways](#18-key-takeaways) |
| 19 | [Interview Questions](#19-interview-questions) |
| 20 | [Section Status](#20-section-status) |
| 21 | [Personal Notes](#21-personal-notes-space-for-your-own-additions) |

---

## 1. What Is a Network?

A **network** is a group of two or more devices connected together so they can communicate and exchange data.

```
Laptop → Router → Internet → Web Server
```

> Networking is the foundation that allows systems to communicate.

<!-- IMAGE SPOT: Simple diagram of "What is a network" -->

---

## 2. Network Components

### 2.1 Topology

A **network topology** describes how devices are arranged and connected.

Common examples: **Star**, **Mesh**, **Tree**

```
             PC
              │
Laptop ─── Switch ─── Printer
              │
            Server
```

### 2.2 Medium

A network needs a **medium** through which data can travel.

| Medium | Example |
|---|---|
| Ethernet | Copper network cable |
| Fiber | Fiber-optic cable |
| Coaxial | Coaxial cable |
| Wireless | Wi-Fi |

### 2.3 Protocol

A **protocol** defines the rules devices use to communicate.

Examples: `TCP`, `UDP`, `IP`

> **Protocol = communication rules for computers.**

<!-- IMAGE SPOT: Topology / medium / protocol illustration -->

---

## 3. Why Networking Matters in Cybersecurity

Networking is central to cybersecurity because suspicious or malicious activity often appears simply as **abnormal network communication**.

```
Printer ───── HTTP ─────► Web Server
```

The important question is **not** whether the connection *works*.

> **The important question is: "Should this communication exist?"**

### Examples of potentially suspicious communication

```
Printer ─────► Internet
Printer ─────► Workstation
Printer ─────► Domain Controller
```

### Security Mindset

> *"Is this connection expected, necessary, and authorized?"*

This mindset matters for SOC analysts, penetration testers, and security engineers alike.

---

## 4. Flat Networks

A **flat network** places many different systems inside the same network.

```
192.168.1.0/24

        Company Network
              │
   ┌──────────┼──────────┐
   │          │          │
  PCs       Servers    Printers
   │          │          │
 Laptops    Database   IP Phones
```

If an attacker compromises one workstation, there may be far more opportunities for **lateral movement**.

```
Attacker
   │
   ▼
Compromised PC
   │
   ├── Printer
   ├── Server
   └── Other PCs
```

<!-- IMAGE SPOT: Flat network vs risk diagram -->

---

## 5. Network Segmentation

**Network segmentation** means dividing a large network into smaller networks.

```
                    Company
                       │
       ┌───────────────┼───────────────┐
       │               │               │
      DMZ          User Network    Server Network
       │               │               │
 Web Servers          PCs          Internal Servers

       ┌───────────────────────────────┐
       │                               │
 Management Network              Printer Network
       │                               │
 Routers/Switches                 Printers
```

### Security Benefits

Segmentation can:

- Reduce the attack surface
- Restrict unnecessary communication
- Limit lateral movement
- Improve monitoring
- Make suspicious traffic easier to identify
- Create additional security boundaries

> Segmentation does **not** make a network invulnerable — it creates additional barriers an attacker must overcome.

---

## 6. Access Control Lists (ACL)

**ACL = Access Control List** — contains rules that control which traffic is allowed or denied.

```
Printer Network
      │
      ├── ❌ Internet
      ├── ❌ Workstations
      ├── ❌ Database
      └── ✅ Print Server
```

### Security Principle

> **Allow what is required. Deny what is unnecessary.**

---

## 7. /24 and /25 Networks

> One of the most important lessons from this section:
> **Do not assume that similar-looking IP addresses belong to the same subnet.**

### /24

```
10.20.0.0/24
Subnet mask: 255.255.255.0
Usable host range: 10.20.0.1 - 10.20.0.254
```

### /25

A `/24` network can be divided into two `/25` networks:

```
10.20.0.0/24
       │
       ├───────────────┐
       ▼               ▼
10.20.0.0/25     10.20.0.128/25
```

| | Network | Usable Hosts |
|---|---|---|
| Network 1 | `10.20.0.0/25` | `10.20.0.1` – `10.20.0.126` |
| Network 2 | `10.20.0.128/25` | `10.20.0.129` – `10.20.0.254` |

**Therefore:** `10.20.0.10` and `10.20.0.200` share the same first three octets — but they are **not** in the same `/25` subnet.

### Always Consider

| Checklist |
|---|
| IP address |
| CIDR / subnet mask |
| Default gateway |
| Routing |
| VLANs |
| ACLs |
| Firewall rules |
| Network segmentation |

<!-- IMAGE SPOT: /24 vs /25 subnet split diagram -->

---

## 8. The Pentester's Oversight

A real-world networking lesson from a penetration-testing scenario:

| Role | Address |
|---|---|
| Server Network | `10.20.0.0/25` |
| Client Network | `10.20.0.128/25` |
| Pentester | `10.20.0.252/24` |

The pentester compromised a client workstation but **failed to correctly understand the network structure**.

```
                    10.20.0.0/24
                           │
              ┌────────────┴────────────┐
              │                         │
       10.20.0.0/25              10.20.0.128/25
       Server Network             Client Network
              │                         │
       Domain Controller             Workstation
```

### Lesson

Before assuming a host is offline, investigate — in order:

```
IP Address
    ↓
Subnet Mask
    ↓
Default Gateway
    ↓
Routing
    ↓
Reachable Networks
    ↓
Firewall / ACL
    ↓
Network Segmentation
```

> A host that does not respond may not be offline — it may simply be on another network or behind a filtering device.

---

## 9. FQDN vs URL

### FQDN — Fully Qualified Domain Name

```
www.hackthebox.eu
```

Identifies a specific host/domain name.

### URL

```
https://www.hackthebox.eu/example?floor=2&office=dev&employee=17
```

| Part | Role |
|---|---|
| `https://` | Scheme / Protocol |
| `www.hackthebox.eu` | FQDN |
| `/example` | Path |
| `?floor=2&office=dev&employee=17` | Query Parameters |

### Easy Memory Trick

> **FQDN** = Which host?
> **URL** = How + which host + which resource + additional parameters

---

## 10. DNS

**DNS = Domain Name System**

Humans prefer names; computers ultimately communicate using IP addresses. DNS translates the name into an IP address.

```
www.example.com
       │
       ▼
      DNS
       │
       ▼
   IP Address
```

### Easy Analogy

> DNS is like the Internet's phonebook.

<!-- IMAGE SPOT: DNS lookup flow diagram -->

---

## 11. Routers

A **router** connects different networks and forwards traffic between them.

```
┌────────────────┐
│  Home Network  │
└───────┬────────┘
        │
        ▼
   ┌──────────┐
   │  Router  │
   └────┬─────┘
        │
        ▼
   ┌──────────┐
   │ Internet │
   └────┬─────┘
        │
        ▼
┌──────────────────┐
│ Company Network  │
└──────────────────┘
```

### Easy Analogy

> Think of a router as a post office that decides where packets should go next.

---

## 12. DMZ

**DMZ = Demilitarized Zone** — a separate network for systems that need to be reachable from less-trusted networks such as the Internet.

```
Internet
    │
    ▼
 Firewall
    │
    ▼
   DMZ
    │
 Web Server
    │
    ▼
Internal Firewall
    │
    ▼
Internal Network
```

### Why use a DMZ?

- A public web server is exposed to Internet traffic.
- If it becomes compromised, segmentation reduces the attacker's ability to directly reach internal systems.

<!-- IMAGE SPOT: DMZ architecture diagram -->

---

## 13. Secure Network Architecture

A secure company network should avoid placing every device into one flat network.

```
                         COMPANY NETWORK
                                │
        ┌───────────────┬───────┼────────┬──────────────┐
        │               │       │        │              │
       DMZ          Users    Servers   Voice       Printers
        │               │       │        │              │
   Web Servers         PCs      DB      Phones        Printers

                        │
                        ▼
                Administration
                     Network
                        │
                 Routers/Switches
```

### 13.1 Web Server — DMZ

The web server ideally sits in a DMZ because:

- Internet users can initiate connections to it.
- It has greater exposure.
- If compromised, segmentation helps protect internal systems.

### 13.2 Workstations — User Network

Workstations should have their own network. Host-based firewall rules can also prevent unnecessary workstation-to-workstation communication, reducing risks such as:

- Lateral movement
- Spoofing
- Man-in-the-middle attacks

### 13.3 Routers & Switches — Administration Network

Network infrastructure should be isolated from normal users, reducing the chance of unauthorized users interacting with management traffic or infrastructure protocols.

> **OSPF Security Concern:** OSPF = Open Shortest Path First. If routing advertisements are not properly trusted or protected, an attacker who injects malicious routing information may influence traffic paths.

### 13.4 IP Phones — Voice Network

IP phones should ideally have their own network. Benefits:

- Reduced opportunities for unauthorized traffic observation
- Easier QoS management
- Better latency control
- Reduced congestion impact

### 13.5 Printers — Printer Network

Printers can contain:

| Printer Attack Surface |
|---|
| Network interfaces |
| Storage |
| Web interfaces |
| Authentication mechanisms |
| Email functionality |
| Scanning functionality |
| Sensitive documents |
| Credentials |
| Firmware |

> Printers should not automatically be trusted just because they are "peripheral" devices.

<!-- IMAGE SPOT: Full secure network architecture diagram -->

---

## 14. IP Phone Segmentation

Voice traffic is sensitive to: **Latency**, **Jitter**, **Congestion**

```
Heavy Network Traffic
        │
        ▼
   Congestion
        │
        ▼
Latency / Jitter
        │
        ▼
Poor Voice Quality
```

> Separating voice traffic makes it easier to apply Quality of Service (QoS) policies.

---

## 15. Printer Security

A printer can be a significant attack surface.

```
Network Access
      │
      ├── Web Interface
      ├── SMB
      ├── Email
      ├── Authentication
      ├── Storage
      └── Firmware
```

> HTB highlights a Windows authentication risk involving **NTLMv2**. A malicious or compromised printer can potentially interact with systems in ways that trigger authentication attempts.

### Recommended Principle

```
Printer Network
      │
      ├── ❌ Internet
      ├── ❌ Random Workstations
      ├── ❌ Admin Systems
      └── ✅ Required Print Services
```

---

## 16. Attack Perspective

An attacker who gains access to a network wants to understand:

```
Where am I?
     ↓
What subnet am I on?
     ↓
What networks can I reach?
     ↓
What hosts exist?
     ↓
What services are available?
     ↓
What segmentation exists?
     ↓
What can I move to next?
```

### Important Questions

- What is my IP?
- What is my subnet?
- What is my default gateway?
- What routes exist?
- Which networks are reachable?
- Are ACLs present?
- Are firewalls filtering traffic?
- Can I perform lateral movement?

### Attacker Goal

```
Initial Access
      ↓
Internal Network
      ↓
Lateral Movement
      ↓
Higher-Value Systems
```

---

## 17. Defense Perspective

A defender should think in the **opposite** direction.

### Important Questions

- Which devices belong in each network?
- Which systems should communicate?
- Which connections are unnecessary?
- Are firewall rules correctly configured?
- Are ACLs restrictive enough?
- Is network traffic being monitored?
- Are IDS/IPS controls deployed?
- Is network documentation accurate?
- Can a compromised workstation reach critical systems?

### Detection Mindset

> **Look for unexpected communication.**

| Normal | Potentially Suspicious |
|---|---|
| Workstation → Web | Printer → Domain Controller |
| Workstation → DNS | Printer → Internet |
| Workstation → Authentication Server | Printer → Random Workstation |

> The second category deserves investigation.

<!-- IMAGE SPOT: Attacker vs Defender flow comparison -->

---

## 18. Key Takeaways

> **IMPORTANT:** IP addresses that look similar do NOT automatically mean hosts are on the same subnet.

- A network allows systems to communicate.
- Topology describes how devices are connected.
- Medium describes how data physically travels.
- Protocols define communication rules.
- Flat networks increase the potential impact of compromise.
- Segmentation creates additional security boundaries.
- ACLs control allowed and denied communication.
- `/24` and `/25` represent different subnet sizes.
- Always verify subnet masks, gateways, and routes.
- DNS maps names to IP addresses.
- Routers forward traffic between networks.
- Public-facing systems can be isolated in a DMZ.
- Workstations, servers, phones, printers, and management systems can be segmented.
- Printers can represent a real security risk.
- Network visibility is critical for both attackers and defenders.

---

## 19. Interview Questions

### Basic

**Q1. What is a computer network?**
A network is a group of connected devices that communicate and exchange data.

**Q2. What is network segmentation?**
Network segmentation divides a larger network into smaller networks to control communication, reduce attack surface, and limit lateral movement.

**Q3. What is a flat network?**
A flat network places many systems in the same network without strong segmentation between them.

**Q4. What is an ACL?**
An Access Control List contains rules that determine which traffic or communication is allowed or denied.

### Practical

**Q5. Two hosts have similar IP addresses but cannot communicate. What would you check?**

- IP addresses
- Subnet masks
- Default gateways
- Routing
- VLAN configuration
- Firewall rules
- ACLs
- Network segmentation

**Q6. Why should a public web server be placed in a DMZ?**
Because the web server is exposed to Internet traffic. A DMZ creates an additional security boundary that can limit the impact if the server is compromised.

**Q7. Why are printers considered a security risk?**
Printers are network-connected devices that may have authentication, storage, web interfaces, email functionality, and access to sensitive information. They can also interact with Windows authentication mechanisms.

### Scenario

**Q8. A printer is communicating with a Domain Controller. What would you do?**

First determine whether the communication is expected. Then investigate:

- Source and destination IP
- Destination port
- Protocol
- Authentication activity
- Firewall/ACL rules
- Printer configuration
- Recent changes
- Related logs

If the communication is unnecessary, restrict it through appropriate network controls.

---

## 20. Section Status

| Category | Status |
|---|---|
| Networking Fundamentals | Done |
| Topologies | Done |
| Network Mediums | Done |
| Protocols | Done |
| Flat Networks | Done |
| Network Segmentation | Done |
| ACLs | Done |
| /24 vs /25 | Done |
| FQDN vs URL | Done |
| DNS | Done |
| Routers | Done |
| DMZ | Done |
| Secure Network Architecture | Done |
| IP Phone Security | Done |
| Printer Security | Done |
| Attacker Perspective | Done |
| Defender Perspective | Done |
| Interview Preparation | Done |

---

## Personal Learning Note

The most important lesson from this section is that networking is not only about making devices communicate.

From a cybersecurity perspective, we need to understand:

> *Who is communicating with whom, over what protocol, from which network, through which path — and whether that communication should exist at all.*

A security professional should be able to move between both perspectives:

```
                NETWORK
                   │
        ┌──────────┴──────────┐
        │                     │
    ATTACKER                DEFENDER
        │                     │
   Find paths             Control paths
   Find hosts             Monitor traffic
   Find services          Detect anomalies
   Move laterally         Limit movement
   Reach targets          Protect targets
```

> Networking knowledge turns raw network traffic into security information.

---

## 21. Personal Notes (space for your own additions)

<!--
  Use this section to add:
  - Your own screenshots / lab diagrams
  - Extra theory you found elsewhere
  - Commands you practiced (ipconfig, traceroute, nslookup, etc.)
  - Links to labs / write-ups

  Example format:

  ### Topic name
  Your notes here...

  ![description](your-image.png)
-->

### (Add your notes below)

---

## Source

Based on the Hack The Box — Junior Cybersecurity Analyst / Introduction to Networking learning material, rewritten into personal study notes and supplemented with practical cybersecurity interpretation.