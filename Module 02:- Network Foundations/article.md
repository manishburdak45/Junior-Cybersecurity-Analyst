# Network Foundations — My Learning (HTB Module 02)

## Introduction

In this module, I started learning the fundamentals of networking. Before this, I only had a basic idea of how the internet works, but this module helped me understand what actually happens behind the scenes when devices communicate.

This is Part 1 of the module, and I will continue adding more sections as I progress.

---

## Understanding What a Network Is

I learned that a network is a collection of interconnected devices that communicate and share data.

To understand it better, I used a simple analogy:
Devices act like people, links act like communication channels, and data is the conversation.

This made it easier to visualize how networks actually work.

---

## Why Networks Are Important

This part helped me understand why networks are essential in real life.

* Devices can share resources like files and printers
* Communication becomes fast and efficient
* Data can be accessed from anywhere
* Teams can collaborate in real time

It showed me that almost everything we do online depends on networks.

---

## Types of Networks

I learned about two main types:

### LAN

A small network used in homes, schools, or offices. It is fast and limited to a small area.

### WAN

A large network that connects multiple LANs across cities or countries. The internet is the best example.

---

## How Internet Connectivity Works

One important concept I understood is how devices connect to the internet:

Devices connect to a router, the router connects to a modem, and the modem connects to the ISP.

This clarified how local networks and global networks are linked together.

---

## Basic Networking Terms

I learned several important terms that are used everywhere in networking:

* IP Address identifies a device logically
* MAC Address identifies a device physically
* DNS converts domain names into IP addresses
* DHCP assigns IP addresses automatically
* Router connects networks
* Switch connects devices within a network
* Bandwidth defines speed
* Latency defines delay

These terms are the foundation of networking.

---

## OSI Model

This was one of the most important topics in this part.

The OSI model has 7 layers, and each layer has a specific role in data communication.

From physical transmission to application-level interaction, everything happens step by step.

Understanding this helped me see how data travels through a network.

---

## TCP/IP Model

I also learned the TCP/IP model, which is used in real-world networking.

It has 4 layers and is simpler than the OSI model.

This made me understand the difference between theoretical models and practical implementation.

---

## Protocols and Communication

I studied some common protocols:

* HTTP and HTTPS for web communication
* FTP for file transfer
* SMTP for email
* DNS for domain resolution
* TCP and UDP for data transmission

I also understood the difference between TCP and UDP:
TCP is reliable and ordered, while UDP is faster but does not guarantee delivery.

---

## Data Transmission

This part explained how data is transmitted:

* Analog and digital transmission
* Communication modes like simplex, half-duplex, and full-duplex
* Wired and wireless media

This helped me understand how data physically moves.

---

## Components of a Network

I learned about different components:

* End devices like computers and phones
* Intermediary devices like routers and switches
* Network media like cables and wireless signals
* Servers that provide services

---

## How Browsing Works

I understood the basic flow when opening a website:

The domain is converted to an IP address using DNS, the request is sent through the network, the server processes it, and the response is returned to the client.

---

## Key Takeaways from Part 1

* Networking is the base of cybersecurity
* Understanding data flow is very important
* OSI model helps in breaking down communication
* TCP/IP is what actually runs the internet
* Every action online follows a structured process

---

## Part 2: DHCP and NAT

In this part of the module, I focused on understanding how devices get IP addresses and how they communicate with the internet.

---

### Understanding DHCP

I learned that DHCP is responsible for automatically assigning IP addresses to devices.

Earlier, I thought IPs were manually configured, but this part made it clear that DHCP handles everything in the background.

It assigns:

* IP address
* Subnet mask
* Gateway
* DNS

This makes network management much easier.

---

### Why DHCP is Needed

Without DHCP:

* Every device needs manual configuration
* IP conflicts can happen
* Managing large networks becomes difficult

With DHCP:

* IP assignment is automatic
* No conflicts
* Easy scalability

---

### DORA Process

One of the most interesting concepts was the DORA process.

When a device connects to a network:

1. It sends a Discover message
2. Server responds with an Offer
3. Device sends a Request
4. Server sends an Acknowledge

This entire process happens within seconds.

---

### IP Lease Concept

I learned that IP addresses are assigned temporarily.

Devices must renew their lease after some time. If they don’t, the IP is released and assigned to another device.

---

### Understanding NAT

NAT was another important concept.

It allows multiple devices in a private network to share a single public IP address.

This solved my confusion about how multiple devices in a home network access the internet using one connection.

---

### Public vs Private IP

I clearly understood that:

* Private IPs are used inside local networks
* Public IPs are used on the internet

Private IPs are not directly accessible from outside.

---

### How NAT Works in Real Life

When a device sends a request:

* Router replaces the private IP with its public IP
* Stores mapping in a NAT table
* When response comes back, it forwards it to the correct device

This process happens continuously.

---

### Types of NAT

I learned three types:

* Static NAT for fixed mapping
* Dynamic NAT using a pool
* PAT where multiple devices share one public IP

PAT is what is commonly used in home networks.

---

### My Understanding

This part helped me understand that:

* DHCP handles IP assignment
* NAT handles internet communication

Both work together to make networking seamless.

---

### Key Takeaways from Part 2

* IP assignment is automatic through DHCP
* DORA explains the full process
* NAT allows multiple devices to share one public IP
* Private IPs are hidden from the internet
* Networking is more automated than it appears

---

## Part 3: DNS and Internet Architecture

In this part, I learned how websites are actually accessed using DNS and how different types of network architectures are designed.

---

### Understanding DNS

I learned that DNS works like a system that converts domain names into IP addresses.

Before this, I only knew that typing a domain opens a website, but now I understand that DNS is responsible for finding the correct IP behind that domain.

---

### Domain vs IP Address

Domain names are easy to remember, while IP addresses are used by machines.

DNS acts as a bridge between these two.

---

### DNS Hierarchy

I understood that DNS follows a structured hierarchy:

* Root servers
* TLD servers
* Authoritative servers

Each level helps in finding the correct IP address.

---

### DNS Resolution Process

When a user enters a website:

* The system checks local cache
* If not found, request goes to DNS server
* It queries root, then TLD, then authoritative server
* Finally, IP address is returned

This entire process happens very quickly.

---

### Understanding Internet Architectures

This part introduced different ways systems are designed.

---

### Peer-to-Peer Architecture

In P2P, devices communicate directly with each other.

There is no central server, and each device can act as both client and server.

This is useful but difficult to manage and secure.

---

### Client-Server Architecture

In this model, clients send requests and servers respond.

This is the most commonly used architecture on the internet.

It provides better control but depends heavily on servers.

---

### Hybrid Architecture

Hybrid combines both P2P and client-server.

It uses central control but allows direct communication between devices.

---

### Cloud Architecture

I learned that cloud computing allows users to access services over the internet without managing hardware.

It provides flexibility and scalability.

---

### Cloud Characteristics

Some important characteristics include:

* On-demand access
* Resource sharing
* Scalability
* Pay-as-you-use model

---

### Software-Defined Networking (SDN)

SDN separates control logic from actual data transfer.

This allows networks to be controlled through software, making them more flexible.

---

### My Understanding

This part helped me understand that:

* DNS is essential for accessing websites
* Internet architecture defines how systems communicate
* Different architectures are used based on requirements

---

### Key Takeaways from Part 3

* DNS translates domain names into IP addresses
* DNS works through multiple layers
* P2P is decentralized while client-server is centralized
* Cloud provides scalable infrastructure
* SDN introduces programmability in networks

---
## Part 4: Network Security and Wireless Networks

In this part, I learned how networks are protected and how wireless communication introduces both convenience and risks.

---

### Understanding Network Security

I understood that network security is not just about blocking attacks, but about protecting data, systems, and users using multiple layers of defense.

It includes controlling access, monitoring activity, and securing communication.

---

### CIA Triad in Practice

This part reinforced the importance of confidentiality, integrity, and availability.

Every security decision is connected to one of these three principles.

---

### Wireless Networks

I learned that wireless communication uses radio waves, which makes it flexible but also less secure than wired networks.

Signals can travel through walls, which increases the risk of interception.

---

### Wireless Trade-offs

* 2.4 GHz provides better coverage but more interference
* 5 GHz provides better speed but shorter range

Choosing the right band depends on the use case.

---

### Wireless Devices

I understood how different devices work:

* Routers manage traffic and provide connectivity
* Mobile hotspots share internet from cellular networks
* Cell towers connect large areas

---

### Firewalls

I learned that firewalls act as the first line of defense.

They filter traffic based on rules and can operate at different layers of the network.

Advanced firewalls can inspect actual data and detect threats.

---

### IDS vs IPS

This was an important concept:

* IDS monitors and alerts
* IPS actively blocks attacks

Both are used together in real environments.

---

### Detection Methods

I learned that systems can detect attacks using:

* Known signatures
* Behavioral anomalies

Each method has its strengths and limitations.

---

### Security Best Practices

This part highlighted that security is not about one tool but a complete strategy.

Important practices include:

* Limiting user access
* Keeping systems updated
* Monitoring logs
* Using layered defense
* Testing security regularly

---

### My Understanding

This part made me realize that:

* Security is a continuous process
* Wireless networks increase risk if not secured properly
* Tools like firewalls and IPS are only effective when configured correctly
* Real security comes from combining multiple techniques

---

### Key Takeaways from Part 4

* Network security is based on layers
* Wireless networks are convenient but vulnerable
* Firewalls control traffic flow
* IDS and IPS serve different roles
* Security depends on both tools and proper configuration

---

### Next Update

I will continue updating this article as I progress further.



