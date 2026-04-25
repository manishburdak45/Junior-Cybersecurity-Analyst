# Module 02: Network Foundations (Part 1)

## Overview

In this part of the HTB Network Foundations module, I learned the basics of networking and how communication actually happens between devices.

This module helped me understand that before using tools or doing any scanning, it is important to clearly understand how networks are structured and how data flows.

---

## What is a Network (My Understanding)

A network is a group of interconnected devices that can communicate with each other and share data.

I understood this using a simple analogy:

* Each device acts like a person
* Communication happens through links
* Data is like the conversation between them
<img width="790" height="817" alt="image" src="https://github.com/user-attachments/assets/28c60ae7-0b65-46e8-85b4-56c9096a3189" />
Credit :- https://academy.hackthebox.com

Main components:

* Nodes (devices like PC, phone, server)
* Links (wired or wireless connections)
* Data sharing (actual communication)
<img width="1595" height="867" alt="image" src="https://github.com/user-attachments/assets/fb2f4810-7c92-465f-ae8d-63cc3d89d50d" />
Credit :- https://academy.hackthebox.com

---

## Why Networks are Important

From this module, I learned that networks are important because they allow:

* Resource sharing (like printers, files)
* Communication (emails, messaging, calls)
* Data access from different locations
* Real-time collaboration
<img width="600" height="300" alt="image" src="https://github.com/user-attachments/assets/ee754cf0-f5b2-44ec-93b5-466ecc7ef864" />
https://expertgraph.com/importance-of-networking-in-business

---

## Types of Networks

I studied two main types:

### LAN (Local Area Network)

* Small area (home, office)
* High speed
* Example: home Wi-Fi

### WAN (Wide Area Network)

* Large area (cities, countries)
* Slower than LAN
* Example: the Internet
<img width="601" height="301" alt="image" src="https://github.com/user-attachments/assets/7092e15d-fb1e-47d0-bf1e-f477c0290c0f" />
https://in.pinterest.com/pin/types-of-computer-network--785948572484015873/
---

## How LAN and WAN Work Together

I understood how devices connect to the internet:

* Devices → Router → Modem → ISP → Internet

The modem plays an important role by converting signals and connecting local networks to the ISP.

---
<img width="1595" height="867" alt="image" src="https://github.com/user-attachments/assets/0a1f4b00-1ce8-4a7d-bdad-dedc5467af28" />
Credit :- https://academy.hackthebox.com
## Basic Network Terminology

Some important terms I learned:

* IP Address → unique identity of a device
* MAC Address → hardware-based address
* DNS → converts domain names into IP addresses
* DHCP → automatically assigns IP addresses
* Router → connects different networks
* Switch → connects devices within a network
* Bandwidth → data transfer capacity
* Latency → delay in communication

---

## OSI Model (7 Layers)

One of the most important concepts I learned is the OSI Model.

It has 7 layers:

1. Physical → data transmission through cables
2. Data Link → MAC addressing and switching
3. Network → routing and IP addressing
4. Transport → data delivery (TCP/UDP)
5. Session → manages connections
6. Presentation → encryption and formatting
7. Application → user-level interaction (HTTP, FTP, etc.)

This model helped me understand how data moves step by step across a network.
<img width="723" height="702" alt="image" src="https://github.com/user-attachments/assets/85d2b903-cecf-45bb-b5d0-87d62db03c32" />
Credit :- https://academy.hackthebox.com
---

## TCP/IP Model (4 Layers)

I also learned the TCP/IP model, which is used in real-world networking:

* Application Layer
* Transport Layer
* Internet Layer
* Link Layer

It is simpler than OSI and used practically in networks.
<img width="765" height="580" alt="image" src="https://github.com/user-attachments/assets/c8168dd7-7ddd-4e7a-9ce7-33d69907776e" />
Credit :- https://academy.hackthebox.com

---

## OSI vs TCP/IP

* OSI → conceptual model (for understanding)
* TCP/IP → practical implementation (used in real systems)
<img width="1284" height="717" alt="image" src="https://github.com/user-attachments/assets/be3ad3de-0916-4a22-a882-67dbdf4b087e" />
Credit :- https://academy.hackthebox.com

---

## Common Protocols

Some important protocols I studied:

* HTTP / HTTPS → web communication
* FTP → file transfer
* SMTP → email sending
* DNS → domain resolution
* TCP → reliable communication
* UDP → fast but less reliable

---

## TCP vs UDP

I clearly understood the difference:

* TCP → reliable, ordered, slower
* UDP → fast, no guarantee, used in streaming

---

## Transmission in Networking

I learned different transmission concepts:

* Transmission types → analog and digital
* Modes → simplex, half-duplex, full-duplex
* Media → wired (Ethernet, fiber) and wireless (Wi-Fi, radio)

---

## Components of a Network

From the later part of the module:

* End devices → computers, phones
* Intermediary devices → routers, switches
* Network media → cables, wireless
* Servers → provide services (web, database, mail)

---

## Important Concepts I Understood

* MAC Address → hardware identity of device
* IP Address → logical identity
* Ports → used to direct traffic (e.g., 80, 443)

---

## How Browsing Works (My Understanding)

When we open a website:

1. DNS converts domain to IP
2. Data is encapsulated
3. Request is sent through network
4. Server processes request
5. Response is sent back

---

## My Key Takeaways

* Networking is the foundation of cybersecurity
* Without understanding networks, tools like scanning do not make sense
* OSI model helps in understanding data flow step by step
* TCP/IP is what actually runs the internet
* Every communication depends on protocols and proper routing

---

## Notes

Detailed notes are available in:
`notes.pdf`

---

## Next Step

In the next part, I will go deeper into networking concepts and how they are used in real-world security scenarios.
# Module 02: Network Foundations (Part 2 — DHCP & NAT)

## Overview

In this part of the module, I focused on two very important networking concepts: DHCP and NAT.

These concepts helped me understand how devices get IP addresses automatically and how private networks communicate with the internet using a single public IP.

---

## DHCP (Dynamic Host Configuration Protocol)

I learned that DHCP is used to automatically assign IP addresses and other network configurations to devices.

Without DHCP, every device would need manual configuration, which is not practical in large networks.

### Key Points I Understood

* Automatically assigns IP addresses
* Prevents IP conflicts
* Reuses unused IPs
* Provides additional configuration (DNS, gateway, subnet)

---

## DHCP Roles

There are two main components:

* DHCP Server → manages IP pool and assigns addresses
* DHCP Client → requests and receives IP configuration

Example: A home router usually acts as a DHCP server.

---

## DORA Process

One of the most important concepts I learned is how DHCP assigns IPs using 4 steps:

* Discover → client looks for DHCP server
* Offer → server offers an IP address
* Request → client requests that IP
* Acknowledge → server confirms assignment

This process happens automatically when a device connects to a network.

---

## IP Lease and Renewal

I understood that IP addresses are not permanent.

* Devices get IPs for a limited time (lease)
* They must renew before expiry
* If not renewed, IP is returned to the pool

---

## NAT (Network Address Translation)

I learned that NAT is used to translate private IP addresses into a public IP address.

This allows multiple devices in a local network to share a single public IP.

---

## Why NAT is Important

NAT solves the IPv4 address shortage problem.

Since public IPs are limited, NAT allows:

* Multiple devices to use one public IP
* Better IP management
* Additional security by hiding internal network

---

## Public vs Private IP

I understood the difference:

* Public IP → globally unique, accessible from internet
* Private IP → used inside local networks

Private IP ranges are defined and cannot be accessed directly from the internet.

---

## How NAT Works

When a device sends a request:

* Private IP is converted into a public IP by the router
* NAT table stores the mapping
* Response is mapped back to the original device

---

## Types of NAT

I learned three types:

* Static NAT → one-to-one mapping
* Dynamic NAT → uses a pool of public IPs
* PAT (NAT Overload) → multiple devices share one public IP using ports

PAT is the most commonly used type in home networks.

---

## NAT Advantages and Limitations

### Advantages

* Saves public IP addresses
* Adds a layer of security
* Allows flexible internal addressing

### Limitations

* Makes troubleshooting harder
* Breaks some protocols
* Requires extra configuration for hosting servers

---

## DHCP vs NAT

I clearly understood the difference:

* DHCP → assigns IP addresses
* NAT → translates IP addresses

Both are essential for modern networking.

---

## Key Takeaways

* DHCP automates IP assignment
* DORA is the process behind DHCP
* NAT enables internet access using a single public IP
* Private IPs remain hidden from the internet
* PAT is the most commonly used NAT type

---

## Notes

Detailed notes are available in:
`Module 2 (Network_foundation) part 2`
