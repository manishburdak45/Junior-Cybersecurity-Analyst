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
<img width="474" height="292" alt="image" src="https://github.com/user-attachments/assets/a380720a-fb10-4866-9396-a1b274ac0f34" />

---

## TCP vs UDP

I clearly understood the difference:

* TCP → reliable, ordered, slower
* UDP → fast, no guarantee, used in streaming
<img width="2000" height="1518" alt="image" src="https://github.com/user-attachments/assets/f72e6e9f-753a-45a5-a971-8cb68f907d34" />

---

## Transmission in Networking

I learned different transmission concepts:

* Transmission types → analog and digital
* Modes → simplex, half-duplex, full-duplex
* Media → wired (Ethernet, fiber) and wireless (Wi-Fi, radio)
 Transmission in Networking
---

## Components of a Network

From the later part of the module:

* End devices → computers, phones
* Intermediary devices → routers, switches
* Network media → cables, wireless
* Servers → provide services (web, database, mail)
<img width="768" height="427" alt="image" src="https://github.com/user-attachments/assets/f0b27ada-311b-4f80-b66a-0dbd4e42593e" />

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
`Module 2 part 2 `

# Module 02: Network Foundations (Part 3 — DNS & Internet Architecture)

## Overview

In this part of the module, I learned how domain names are translated into IP addresses using DNS and how different internet architectures work.

This helped me understand what happens behind the scenes when we access a website and how modern systems are designed.

---

## DNS (Domain Name System)

I learned that DNS works like a translator between human-readable domain names and machine-readable IP addresses.

Instead of remembering IP addresses, we use domain names, and DNS resolves them.
<img width="672" height="352" alt="image" src="https://github.com/user-attachments/assets/0240b445-92ac-42cd-8645-2e8df7b93839" />
https://academy.hackthebox.com
---

## Domain Names vs IP Addresses

* Domain names are easy for humans to remember
* IP addresses are used by machines for communication

DNS connects both by translating names into IP addresses.

---

## DNS Hierarchy

I understood that DNS works in a hierarchical structure:

* Root Servers
* Top-Level Domains (TLDs like .com, .org)
* Second-Level Domains (example.com)
* Subdomains (www, mail, etc.)

---

## DNS Resolution Process

When a user enters a domain:

1. Browser checks local cache
2. Request goes to recursive DNS server
3. Root server is queried
4. TLD server is contacted
5. Authoritative server returns IP
6. Browser connects to the website

This entire process happens very quickly.
<img width="1347" height="625" alt="image" src="https://github.com/user-attachments/assets/0c0273f3-7665-459a-a0de-a01900e5fcd7" />
https://academy.hackthebox.com
---

## Internet Architecture Overview

I studied different types of architectures used in networking:

* Peer-to-Peer (P2P)
* Client-Server
* Hybrid
* Cloud
* Software-Defined Networking (SDN)

Each has different use cases and trade-offs.

---

## Peer-to-Peer (P2P)

* Devices act as both client and server
* No central authority
* Used in file sharing and blockchain

### Advantages

* Scalable
* No single point of failure

### Disadvantages

* Hard to manage
* Security risks

---

## Client-Server Architecture

* Clients send requests
* Servers respond with data

Used in websites, email systems, and applications.

### Advantages

* Centralized control
* Better security management

### Disadvantages

* Single point of failure
* High cost and maintenance

---

## Tier Models

I learned about different layers in client-server systems:

* Single-tier
* Two-tier
* Three-tier
* N-tier

More tiers improve scalability and separation of responsibilities.

---

## Hybrid Architecture

Combines client-server and P2P.

* Central server handles control
* Data transfer can happen between peers

Used in video conferencing and messaging systems.

---

## Cloud Architecture

Cloud services are provided over the internet by third-party providers.

Examples include AWS, Azure, and Google Cloud.

### Advantages

* Scalable
* Flexible
* No need to manage hardware

### Disadvantages

* Dependency on provider
* Requires stable internet

---

## Cloud Characteristics

I learned five key features:

* On-demand self-service
* Broad network access
* Resource pooling
* Rapid elasticity
* Measured service

---

## Software-Defined Networking (SDN)

SDN separates control and data planes.

* Control plane decides routing
* Data plane forwards traffic

This makes networks programmable and easier to manage.

---

## Architecture Comparison

I understood that:

* P2P is decentralized
* Client-server is centralized
* Hybrid combines both
* Cloud is provider-managed
* SDN is software-controlled

---

## Key Takeaways

* DNS translates domain names into IP addresses
* DNS works through a hierarchical system
* Internet uses multiple architectures
* Each architecture has advantages and trade-offs
* Understanding architecture is important for cybersecurity

---

## Notes

Detailed notes are available in:
`module 2 part 3 .pdf`

# Module 02: Network Foundations (Part 4 — Network Security & Wireless Networks)

## Overview

In this part of the module, I learned how networks are protected using different security mechanisms and how wireless communication works along with its risks.

This part connected networking concepts with real-world security practices.

---

## What is Network Security

I learned that network security is about protecting data, devices, and users within a network.

It involves:

* Access control
* Data protection
* Monitoring and detection
<img width="288" height="175" alt="image" src="https://github.com/user-attachments/assets/a1f4112b-6f7c-4916-8b49-1c30b6f3eb72" />

It works like a layered defense system.

---

## CIA Triad in Network Security

Network security is based on:

* Confidentiality → only authorized access
* Integrity → data should not be altered
* Availability → systems should remain accessible
<img width="290" height="174" alt="image" src="https://github.com/user-attachments/assets/7d018ab2-3d5e-4990-93ce-b889b6276e5f" />

---

## Wireless Networks

Wireless networks allow devices to communicate without cables using radio signals.
<img width="300" height="168" alt="image" src="https://github.com/user-attachments/assets/928f5554-4126-41a7-b664-dd174d4d284a" />

### Advantages

* Mobility
* Easy setup
* Scalability

### Disadvantages

* Interference
* Security risks
* Slower than wired

---

## Wireless Frequency Bands

I understood different bands:

* 2.4 GHz → longer range but more interference
* 5 GHz → faster speed but shorter range
* Cellular → wide coverage using towers
<img width="352" height="143" alt="image" src="https://github.com/user-attachments/assets/46cd1a3c-636b-443b-a7e2-1a8645d5811e" />

---

## Key Wireless Devices

* Router → manages traffic and provides Wi-Fi
* Mobile Hotspot → shares cellular internet
* Cell Tower → connects devices to telecom network

---

## Firewalls

I learned that a firewall controls incoming and outgoing traffic based on rules.

Types include:

* Packet Filtering → basic filtering using IP and ports
* Stateful Inspection → tracks connections
* Application Layer Firewall → inspects content
* Next-Gen Firewall → advanced inspection and threat detection
<img width="303" height="166" alt="image" src="https://github.com/user-attachments/assets/ea4bf1a8-804d-4061-a272-d646dd4e1190" />

---

## IDS vs IPS

I understood the difference:

* IDS → detects and alerts
* IPS → actively blocks threats

Both are important for monitoring and protection.
<img width="264" height="191" alt="image" src="https://github.com/user-attachments/assets/e092ef6a-fcac-4d73-862b-91cffd58d007" />

---

## Detection Techniques

* Signature-based → detects known attacks
* Anomaly-based → detects unusual behavior
* Network vs Host-based systems

---

## Security Best Practices

I learned important practices:

* Principle of Least Privilege
* Regular updates and patching
* Monitoring and logging
* Defense in depth
* Penetration testing

---

## Key Takeaways

* Network security uses layered defense
* Wireless networks are convenient but risky
* Firewalls control traffic
* IDS detects while IPS prevents
* Security requires continuous monitoring

---

## Notes

Detailed notes are available in:
`notes.pdf`
# Module 02: Network Foundations (Final Part — Practical Networking & Skills Assessment)

## Overview

In this final part of the module, I applied networking concepts in a practical lab environment using HTB Pwnbox.

This section focused on real tools, commands, and how networking works in real scenarios.

---

## Lab Environment

* Platform: HTB Academy (Pwnbox — Parrot OS)
* Access: Browser-based virtual machine
* Focus: Real-world networking commands and analysis

---

## Network Interfaces

I learned how to inspect network interfaces using tools like:

* ifconfig
* ip route

### Key Interfaces

* ens3 → main network interface (public IP)
* lo → loopback (127.0.0.1)
* tun0 → VPN interface used to connect to HTB lab

---

## Loopback Interface

I understood that:

* 127.0.0.1 is used for internal communication
* It never leaves the system
* Used for local services like databases

---

## Checking Open Ports

Using:

* netstat

I learned how to identify:

* Open ports
* Listening services
* Running processes

---

## Port Forwarding

I understood how internal services can be exposed externally using port forwarding.

Example:

* Web browser → HTTP → redirected to internal VNC service

---

## VPN and tun0 Interface

I learned how VPN works using tun0:

* Creates a virtual network interface
* Allows access to remote lab machines
* Works like being in the same network

---

## Testing Connectivity

Using:

* ping

I understood:

* Reachability of a system
* Latency (time delay)
* Packet loss

---

## Port Scanning

Using:

* nmap

I learned how to:

* Identify open ports
* Detect services
* Gather information about a target

---

## Common Open Ports

Examples observed:

* 21 → FTP
* 80 → HTTP
* 445 → SMB
* 3389 → RDP

These help in identifying system behavior.

---

## Protocol Interaction

### FTP

Using netcat:

* Connected to FTP service
* Used commands like USER, PASS, LIST, RETR
* Understood control and data channels

---

### HTTP

Using netcat:

* Sent manual HTTP requests
* Learned headers like Host and User-Agent
* Understood how servers respond

---

## Data Flow (End-to-End)

I understood how data travels:

1. Device connects to network (DHCP)
2. DNS resolves domain
3. Request is created (HTTP)
4. Data passes through layers (OSI model)
5. NAT translates IP
6. Server responds
7. Data is received and rendered

---

## Key Takeaways

* Networking concepts are best understood through practice
* Tools like nmap and netstat reveal system details
* VPN creates secure access to remote networks
* Protocols like HTTP and FTP can be manually tested
* Understanding data flow is critical in cybersecurity

---

## Notes

Detailed notes available in:
`notes.pdf`


