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

### Next Update

I will continue adding more concepts as I move forward in the module.

