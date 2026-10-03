# Section B — Network Appearance & Connectivity

## Introduction

This section focuses on three connected questions:

1. How do computer networks **look** when they are represented on paper?
2. How can computers be **connected** to one another?
3. How do connectivity structures evolve from a simple direct connection between two computers into scalable, interconnected networks?

The discussion follows a natural progression. It begins with the most basic picture of a network: systems drawn as nodes joined by links. It then examines the simplest possible connection, **Point-to-Point** connectivity. It then shows how connecting many computers introduces new problems, which are studied through **Bus** and **Full Mesh** topologies. Finally, it presents **indirect connectivity**, **Star** and **Ring** topologies, and the idea of **Inter-Networks** or **Networks of Networks**, which is the structural idea behind the **Internet**.

### Convention used in this document

- Content explained under the main numbered headings is **lecture-derived** and follows the terminology of the source material.
- Any extra technical knowledge that goes beyond the source is placed under a clearly marked heading or label: **Additional Understanding**.
- Link counts for Star and Ring topologies, and all protocol-level remarks, are given only under **Additional Understanding** unless stated otherwise.

---

## Topic 1 — Basic Network Representation

### 1. Concept

A computer network is drawn as a collection of **systems** (computers, devices) that are joined by **communication links**. In this representation:

- Each system is shown as a **node**.
- Each communication link is shown as a **line** (an edge) between two nodes.
- The picture shows **who is connected to whom**. It does not, by itself, show **what is being communicated** or **how** it is communicated.

This is the basic answer to the question "How do computer networks look like?": a set of connected systems.

> **Key point:** A network diagram describes **structure** (connectivity). Communication (data, rules, addressing, delivery) is a separate concern.

### 2. Simple Network Representation

A single connection between two systems:

```
A ───────── B
```

A small network of several systems:

```
A ───────── B
│           │
│           │
D ───────── C
```

A network can also be drawn abstractly, as a cloud with systems attached:

```
    A        B
     \      /
      \    /
    (  NETWORK  )
      /    \
     /      \
    C        D
```

In all three drawings, the same two ideas appear: **nodes** and **connections**.

### 3. Important Terminology

| Term | Meaning |
|---|---|
| **Network** | A group of systems that are connected so that they can communicate. |
| **System / Computer / Host** | An entity that takes part in the network. |
| **Node** | A system as drawn in a network diagram. |
| **Link** | A communication connection between two nodes. |
| **Connectivity** | The way nodes are joined together by links. |
| **Direct connectivity** | Two systems are joined by a link between them. |
| **Topology** | The pattern or shape in which nodes and links are arranged (for example Bus, Full Mesh, Star, Ring). |

### 4. How Connectivity Works

**Direct connectivity** is the starting point. If system A must communicate with system B, the most obvious solution is to connect A and B with a link.

```
A ───────── B
```

With two systems, one link is enough. With more systems, the same idea can be extended: connect every system directly to every other system. For example, with three systems:

```
        A
       / \
      /   \
     B ─── C
```

Each pair has its own dedicated link. This works for very small networks, but the number of links begins to grow quickly as systems are added.

### 5. Scaling Problem

Increasing the number of systems creates a connectivity problem. If every system must be connected directly to every other system:

| Number of systems (N) | Direct links required |
|---|---|
| 2 | 1 |
| 3 | 3 |
| 4 | 6 |
| 5 | 10 |

The number of links grows much faster than the number of systems. This is the **scaling problem** of connectivity: a structure that is acceptable for two or three systems becomes impractical for many systems. This motivates the later study of shared media (Bus) and of indirect connectivity (Star, Ring, Inter-Networks).

**Structure versus communication**

| Aspect | Understanding network structure | Understanding communication |
|---|---|---|
| Question answered | Which systems are connected, and how? | How does information actually move between systems? |
| Typical tools | Node-and-link diagrams, topologies | Addressing, signals, rules for exchanging data |
| Example | "A is linked to B and C." | "A sends a message intended for C." |

A network diagram on its own tells us the first, not the second. Both are needed to understand a network completely.

### 6. Practical Understanding

- Whenever a network is described, it is useful to first sketch it: identify the nodes, then identify the links.
- A diagram helps to estimate how many links, cables, or ports a design would need.
- Network structure determines how many connections must be built, maintained, and managed. A design that needs too many links is expensive and difficult to manage.

#### Additional Understanding

- In practice, network diagrams exist at several levels: a **physical diagram** shows cables and devices, while a **logical diagram** shows how systems are grouped and addressed. The simple node-and-link picture can represent either.
- Links may be wired (copper, fiber) or wireless. The node-and-link abstraction is the same regardless of the medium.

### 7. Cybersecurity Connection

- **Visibility:** A network diagram is the first tool for asset discovery. A defender cannot protect a system that does not appear on the map.
- **Attack surface:** Every node and every link is a potential point of attack. More links and nodes generally mean a larger attack surface.
- **Path analysis:** Knowing which nodes are connected shows which systems an attacker could reach after compromising one node.

### 8. Exam Answer

**Question:** Explain how a basic computer network is represented, and describe the scaling problem that appears when the number of systems increases. (6 marks)

**Answer:**

A computer network is represented as a set of **systems (nodes)** joined by **communication links**. Each system is drawn as a point and each link as a line between two points. The diagram shows the **connectivity structure** of the network, that is, which systems are connected to which.

The simplest case is **direct connectivity**, where two systems are connected by a single link:

```
A ───────── B
```

When more systems are added, the direct approach requires a separate link for every pair of systems. For N systems the number of links is N(N − 1) / 2, which gives 1 link for 2 systems, 3 links for 3 systems, 6 links for 4 systems, and 10 links for 5 systems. The number of links grows much faster than the number of systems, so direct connection of every pair does not scale. This is the scaling problem of connectivity.

It is also important to distinguish **network structure** (who is connected to whom) from **communication** (how information is exchanged between systems). A structural diagram alone does not describe communication.

### 9. Quick Revision

- A network is drawn as **nodes** (systems) connected by **links**.
- A diagram shows **structure**, not communication.
- **Direct connectivity**: systems joined by a link between them.
- Direct links for every pair: 2 systems need 1 link, 3 need 3, 4 need 6, 5 need 10.
- More systems cause a **scaling problem**.

### 10. Knowledge Check

1. What do nodes and links represent in a network diagram?
   *Answer: Nodes represent systems; links represent communication connections between them.*
2. Does a network diagram describe how data is exchanged?
   *Answer: No. It describes structure (connectivity). Communication is a separate topic.*
3. How many direct links are needed to connect 5 systems pairwise?
   *Answer: 10.*
4. Why is direct connection of every pair of systems a problem for large networks?
   *Answer: The number of links grows much faster than the number of systems.*

---

## Topic 2 — Point-to-Point Connectivity

### 1. Definition

**Point-to-Point connectivity** is the simplest form of network connection. Exactly **two endpoints** are connected **directly** to each other, and communication takes place directly between those two endpoints.

```
A ───────── B
```

There is nothing between A and B, and no other system shares the connection.

### 2. Basic Structure

| Element | Description |
|---|---|
| Endpoint 1 | The first system (A). |
| Endpoint 2 | The second system (B). |
| Link | A direct connection between A and B only. |

Properties of the structure:

- Only **two** systems are involved.
- The connection is **dedicated** to those two systems.
- Whatever one endpoint sends is intended for the only other endpoint.

### 3. Why Addressing and Routing Are Not Required in the Simple Model

The lecture presents Point-to-Point connectivity as a **simplified direct-connection model**. In this model, functions such as **addressing**, **routing**, and **protocol** are not needed. The reasoning follows directly from the structure:

| Function | Purpose in general | Why it is not needed in the simple model |
|---|---|---|
| **Addressing** | To identify which system is the intended receiver among several systems. | There is only one other system on the link, so the receiver is unambiguous. |
| **Routing** | To choose a path through the network toward the destination. | There is only one possible path: the direct link. |
| **Protocol** | To define rules for communication among many systems and situations. | In the simplified model, the two endpoints only need to move signals across the link. |

The core idea is that these functions exist to resolve choices (which receiver, which path). A Point-to-Point link offers no such choices.

> **Important:** This statement describes the lecture's simplified model. It should not be read as a claim about every real-world point-to-point link. See **Additional Understanding** in this topic.

### 4. Physical Layer

In the simple Point-to-Point model, the main concern is the **physical layer**, which deals with how information is physically transferred across the link between the two endpoints. Because the two endpoints are directly connected, the task reduces to sending information across the medium and receiving it correctly at the other end.

### 5. Coding and Modulation

Two key physical-layer concepts appear in this model:

- **Coding:** Information is represented in a form suitable for transmission (for example, representing data as sequences of signal values).
- **Modulation:** The information is carried on a signal that is suitable for the medium by varying characteristics of that signal.

```
 Information ──► Coding ──► Modulation ──► Medium ──► (receiver reverses the process)
   at A                                                        at B
```

#### Additional Understanding

- Coding and modulation are treated in more detail in physical-layer studies. Examples of modulation are changing the amplitude, frequency, or phase of a carrier signal.
- The receiver performs the reverse operations (demodulation and decoding) to recover the original information.

### 6. Advantages

- **Simplicity:** Only two endpoints and one link.
- **Unambiguous communication:** Whatever is sent reaches the only other endpoint.
- **No sharing:** The link is not shared with other systems, so there is no contention among several senders.
- **No path selection:** Only one path exists.

### 7. Limitations

- It connects **only two** systems.
- It does **not solve large-network scalability**. To connect many systems, a separate Point-to-Point link would be needed for every pair, which leads to N(N − 1) / 2 links for N systems.
- Real networks contain many systems, so a model that serves exactly two endpoints is not sufficient on its own.

### 8. Comparison with Multi-Computer Connectivity

| Feature | Point-to-Point | Multi-Computer Network |
|---|---|---|
| Number of endpoints | Two | Many |
| Intended receiver | Obvious (the other end) | Must be identified (addressing becomes necessary) |
| Path | Single direct link | Several possible arrangements and paths |
| Sharing of the medium | None | May be shared (for example Bus) |
| Collisions | Not a concern in the simple model | Possible when the medium is shared |
| Main concern | Physical transfer (coding, modulation) | Addressing, access to the medium, scalability |
| Scalability | Not applicable beyond two systems | Central design problem |

Diagrams:

```
Point-to-Point                   Multi-computer (shared medium)

A ───────── B                    A ───┐
                                 B ───┤
                                 C ───┤──── Shared Bus
                                 D ───┘
```

### 9. Additional Understanding

The following points are general networking knowledge and are **not** part of the lecture's simplified model. They are included only to clarify that the simplified model must not be over-generalized.

- Real point-to-point links (for example, a leased line between two sites, or a serial link between two devices) commonly use **protocols** for tasks such as framing, error detection, link management, and authentication. Examples include PPP and HDLC.
- Even a link with only two ends may form part of a larger network, in which case **addressing and routing** are needed for the traffic that crosses that link toward further destinations.
- Therefore the correct reading is: in the **simplified model of two directly connected endpoints**, addressing and routing have no role to play because there is no choice of receiver or path. This does not mean that real systems never use protocols on such links.

### 10. Cybersecurity Connection

- **Physical access:** Even a dedicated link can be tapped or damaged if an attacker gains access to the medium. Physical security of cables and equipment remains important.
- **No sharing, smaller exposure:** Because only two endpoints exist, there are no other participants on the link who could overhear traffic. This reduces exposure compared with shared media.
- **Endpoint trust:** Security depends on the integrity of the two endpoints themselves. If one endpoint is compromised, the whole connection is compromised.
- **Confidentiality in transit:** Direct connectivity does not automatically provide encryption. Protection of data on the link is a separate matter (see Additional Understanding in this topic for the idea that real links use further mechanisms).

### 11. Exam Answer

**Question:** Define Point-to-Point connectivity. Explain why addressing and routing are not required in this simple configuration, and state why it does not solve the scalability problem of large networks. (6 marks)

**Answer:**

**Point-to-Point connectivity** is a network configuration in which exactly **two endpoints** are connected **directly** by a link and communicate directly with each other.

```
A ───────── B
```

In this simple configuration, **addressing** is not needed because there is only one other endpoint, so the intended receiver is always known. **Routing** is not needed because there is only one possible path, the direct link. A communication **protocol** is likewise not required in this simplified model, because the endpoints only need to transfer information across the link. The main concern is the **physical layer**, which includes **coding** (representing information for transmission) and **modulation** (carrying the information on a signal suitable for the medium).

Point-to-Point connectivity is simple, but it does not solve large-network scalability. It connects only two systems. To connect N systems using Point-to-Point links between every pair, N(N − 1) / 2 links are required, which grows rapidly as N increases. Therefore other connectivity structures are needed for many systems.

*Note:* This describes the simplified model. Real point-to-point links may still use protocols for functions such as framing and error handling.

### 12. Quick Revision

- **Point-to-Point:** two endpoints, connected directly.
- Communication is **direct** between the two endpoints.
- No addressing, routing, or protocol is needed **in the simple model** because there are no choices to make.
- The main concern is the **physical layer**: **coding** and **modulation**.
- Simple, but does **not** scale to many systems.
- Real links may still use protocols (Additional Understanding).

### 13. Knowledge Check

1. How many endpoints does a Point-to-Point connection have?
   *Answer: Two.*
2. Why is addressing unnecessary in the simple Point-to-Point model?
   *Answer: There is only one possible receiver.*
3. Name the two physical-layer concepts associated with this model.
   *Answer: Coding and modulation.*
4. Why does Point-to-Point connectivity not solve large-network scalability?
   *Answer: It connects only two systems; connecting many systems pairwise would require a very large number of links.*
5. Is it correct to say that real point-to-point links never use protocols?
   *Answer: No. That is a property of the simplified model only. Real links often use protocols.*

---

## Topic 3 — Connecting Multiple Users / Computers

### 1. Need for Multiple-Computer Connectivity

Connecting **two** computers is simple, as shown by Point-to-Point connectivity. Connecting **N** users or computers is a different problem because new questions arise:

- How should all the computers be physically joined together?
- When one computer sends information, **which** computer is the **intended receiver**?
- What happens if several computers try to send at the same time?
- How many links are needed, and is that number reasonable?

Two basic answers are examined: **Bus topology** (all computers share one medium) and **Full Mesh topology** (every pair of computers has a direct link).

### 2. Bus Topology

In a **Bus topology**, all computers are attached to **one common communication medium**.

```
A ───┐
B ───┤
C ───┤──── Shared Bus
D ───┘
```

An alternative drawing of the same idea:

```
 A     B     C     D
 │     │     │     │
─┴─────┴─────┴─────┴─────  Shared Bus
```

Main characteristic: only **one** communication medium is used by **all** computers, so the number of links needed is small (each computer needs only one attachment to the bus).

### 3. Shared Medium

The bus is a **shared medium**:

- Every computer uses the same medium to send information.
- The capacity of the medium (its bandwidth) must be **shared** among all the computers using it.
- When one computer is using the medium, the medium is not available to others in the same way.

Sharing makes the structure economical in links, but it creates the problems described in the following sections.

### 4. Broadcast

The shared medium has a **broadcast nature**: what one computer places on the medium is available to **all** computers attached to it.

```
Sender A transmits on the bus:

A ───┐
B ───┤◄──── signal reaches B
C ───┤◄──── signal reaches C
D ───┘◄──── signal reaches D
```

If A intends to send information to C only, the signal still reaches B and D, because the medium is shared by all. The medium itself does not restrict the signal to the intended receiver.

### 5. Addressing

Because of the broadcast nature of the shared medium, **addressing** becomes necessary.

- Every computer needs an **identity**, called an **address**.
- The sender includes the address of the **intended receiver** with the information.
- Each computer examines the address to decide whether the information is meant for it.

```
A sends:  [ To: C | Information ]

B sees it ──► address is not B  ──► not for me
C sees it ──► address is C      ──► for me
D sees it ──► address is not D  ──► not for me
```

Contrast with Point-to-Point connectivity: there, the receiver is obvious, so addressing is not needed in the simple model. On a shared medium the receiver is not obvious, so addressing is needed.

### 6. Collisions

A **collision** occurs when **two or more computers transmit at the same time on the shared medium**. Their signals overlap, and the information becomes unusable for the receivers.

```
Time ─────────────────────────────►

A:   ████████
B:        ████████
          ^^^^^^
          overlap on the shared bus = collision
```

Key points:

- Collisions are a direct consequence of **sharing** a medium among many computers.
- A collision wastes the shared bandwidth, because the overlapping transmissions are not successfully delivered.
- Something must therefore **control access** to the medium.

### 7. Media Access Control (MAC)

**Media Access Control (MAC)** is the name for the mechanism that **controls how computers access the shared medium**, so that collisions are avoided or managed.

- It answers the question: **who may use the medium, and when?**
- It is needed because the medium is shared and collisions are possible.

At the level of this lecture, the important idea is the **need** for MAC and its **purpose**, not the details of specific MAC protocols.

### 8. Full Mesh Topology

In a **Full Mesh topology**, **every computer is connected directly to every other computer**. Every pair of computers has its own dedicated link.

Full Mesh with 4 computers:

```
A ─────────── B
│ ╲         ╱ │
│   ╲     ╱   │
│     ╲ ╱     │
│     ╱ ╲     │
│   ╱     ╲   │
│ ╱         ╲ │
D ─────────── C
```

Links in this drawing: A-B, A-C, A-D, B-C, B-D, C-D, a total of 6.

**Advantages of direct connectivity in Full Mesh**

- Each pair of computers communicates over its **own dedicated link**, so the link is not shared with other pairs.
- The receiver is determined by **which link** is used, and the link is not shared among all computers.
- Because links are not shared, the collision problem of the shared bus does not arise in the same way.
- Each link's capacity is available to the two computers it joins.

### 9. Full Mesh Link Calculation

For N computers, the number of links required in a Full Mesh is:

```
Links = N(N − 1) / 2
```

**Where N is the number of computers (nodes).**

**Reasoning:**

1. Each of the N computers connects to the other (N − 1) computers.
2. Counting from every computer gives N × (N − 1) connection ends.
3. Each link has two ends and would otherwise be counted twice, so divide by 2.

**Numerical examples**

| N | Calculation | Links |
|---|---|---|
| 2 | 2 × 1 / 2 | 1 |
| 3 | 3 × 2 / 2 | 3 |
| 4 | 4 × 3 / 2 | 6 |
| 5 | 5 × 4 / 2 | 10 |

**Additional numerical examples (Additional Understanding):**

| N | Calculation | Links |
|---|---|---|
| 10 | 10 × 9 / 2 | 45 |
| 100 | 100 × 99 / 2 | 4,950 |
| 1,000 | 1,000 × 999 / 2 | 499,500 |

Each computer in a Full Mesh also needs (N − 1) connections of its own, which is a further practical consideration (Additional Understanding).

### 10. Scalability Problem

The Full Mesh provides direct connectivity between every pair, but the **number of links grows roughly with the square of N**. Doubling the number of computers roughly **quadruples** the number of links.

Consequences:

- The number of links becomes very large for even moderate N.
- Building, maintaining, and managing so many links is impractical.
- Full Mesh is therefore **inefficient in the number of links**.

### 11. Bus vs Full Mesh

The lecture's key comparison is that the two structures are inefficient in **different** ways:

- **Mesh** is inefficient in the **number of links**.
- **Bus** is inefficient in the **use of bandwidth** (the shared medium).

| Feature | Bus | Full Mesh |
|---|---|---|
| Structure | All computers attached to one shared medium | Every pair of computers has a direct link |
| Number of links | Very small (one shared medium) | N(N − 1) / 2 |
| Medium sharing | Shared by all computers | Each link used by only two computers |
| Broadcast | Yes, signals reach all computers | Not by nature; each link joins only two computers |
| Addressing | Necessary (to identify intended receiver) | The link used identifies the receiver in the simple view |
| Collisions | Possible, because the medium is shared | Not a concern from medium sharing |
| MAC | Needed | Not needed for link sharing |
| Main inefficiency | Bandwidth / shared-medium usage | Number of links |
| Scalability | Limited by sharing of bandwidth | Limited by growth in links |

Neither structure is satisfactory for large networks, which motivates indirect connectivity (Topic 4).

### 12. Additional Understanding

The following points are general networking knowledge and are not drawn from the lecture.

- **Examples of MAC approaches:** Controlled access methods (such as token passing) and contention-based methods (such as CSMA/CD in classic Ethernet on a shared bus) are examples of how access to a shared medium can be managed. They are not required for the lecture-level understanding.
- **Physical addresses:** In modern LANs, the address used on the local medium is commonly called a **MAC address**. The term "MAC" in that context refers to the same Media Access Control concept.
- **Modern Ethernet:** Modern wired LANs typically do not use a single shared bus. Devices are connected through a central device, which greatly reduces collisions (see Star topology in Topic 4).
- **Partial mesh:** Real networks that need redundancy often use a **partial mesh**, connecting only selected pairs directly, rather than a Full Mesh.
- **Ports per computer:** In a Full Mesh, each computer needs (N − 1) interfaces or connections, which adds cost and complexity.

### 13. Cybersecurity Connection

- **Eavesdropping on a Bus:** Because of the broadcast nature of the shared medium, every attached computer can in principle receive signals meant for others. A malicious or compromised computer can listen to traffic not addressed to it. Addressing helps well-behaved computers ignore such traffic, but it does not prevent a hostile computer from reading it.
- **Disruption through collisions:** A computer that transmits continually can prevent others from using the shared medium, creating a form of denial of service on a shared bus.
- **Address abuse:** Because addressing identifies the intended receiver, a computer that uses another computer's address may try to impersonate it (address spoofing).
- **Full Mesh attack surface:** Many links means many connection points to secure and monitor. Compromise of one node gives a direct path to every other node.
- **Confidentiality:** Dedicated links in a Full Mesh reduce the exposure that comes from a shared broadcast medium, but they do not by themselves provide encryption.

### 14. Exam Answer

**Question:** Explain Bus topology and Full Mesh topology for connecting N computers. Discuss broadcast, addressing, collisions, and MAC in the Bus, and calculate the number of links in a Full Mesh. Compare the two. (10 marks)

**Answer:**

**Bus topology.** In a Bus topology, all computers are connected to a **single shared communication medium**.

```
A ───┐
B ───┤
C ───┤──── Shared Bus
D ───┘
```

- **Broadcast:** The medium is shared, so a signal sent by one computer reaches all other computers.
- **Addressing:** Since every computer receives the signal, each computer needs an address, and the sender must indicate the **intended receiver**, so that only that computer accepts the information.
- **Collision:** If two or more computers transmit at the same time, their signals overlap and the information is lost. This is a **collision**.
- **Media Access Control (MAC):** To manage this, a mechanism is required to control **how computers access the shared medium**. This mechanism is called Media Access Control.

**Full Mesh topology.** In a Full Mesh, **every computer is connected directly to every other computer**.

```
A ─────────── B
│ ╲         ╱ │
│   ╲     ╱   │
│     ╲ ╱     │
│     ╱ ╲     │
│   ╱     ╲   │
│ ╱         ╲ │
D ─────────── C
```

The number of links is:

```
Links = N(N − 1) / 2
```

For example, N = 2 gives 1 link, N = 3 gives 3 links, N = 4 gives 6 links, and N = 5 gives 10 links. The number of links grows approximately with N squared, so Full Mesh does not scale.

**Comparison.**

| Feature | Bus | Full Mesh |
|---|---|---|
| Links | Few (one shared medium) | N(N − 1) / 2 |
| Medium | Shared, broadcast | Dedicated link per pair |
| Collisions / MAC | Yes | Not due to sharing |
| Inefficiency | Bandwidth (shared medium) | Number of links |

**Conclusion.** Mesh is inefficient in the number of links, while Bus is inefficient in bandwidth usage. Neither is suitable for large networks, which motivates indirect connectivity.

### 15. Quick Revision

- Connecting N computers is harder than connecting two.
- **Bus:** one **shared medium** for all computers.
- Shared medium has a **broadcast** nature: all computers receive the signal.
- **Addressing** is needed to identify the **intended receiver**.
- **Collision:** simultaneous transmissions overlap on the shared medium.
- **MAC:** controls access to the shared medium.
- **Full Mesh:** every pair directly connected.
- **Links = N(N − 1) / 2** (N = 2: 1, N = 3: 3, N = 4: 6, N = 5: 10).
- **Mesh** is inefficient in links; **Bus** is inefficient in bandwidth.

### 16. Knowledge Check

1. What is meant by the broadcast nature of a shared medium?
   *Answer: A signal sent by one computer is available to all computers attached to the medium.*
2. Why is addressing necessary on a Bus?
   *Answer: All computers receive the signal, so the intended receiver must be identified.*
3. Define a collision.
   *Answer: The overlap of simultaneous transmissions on a shared medium, which makes the information unusable.*
4. What is the purpose of MAC?
   *Answer: To control how computers access the shared medium.*
5. How many links does a Full Mesh of 6 computers need?
   *Answer: 6 × 5 / 2 = 15.*
6. In one phrase each, what is the main inefficiency of Mesh and of Bus?
   *Answer: Mesh: number of links. Bus: bandwidth (shared-medium usage).*

---

## Topic 4 — Scalable / Indirect Connectivity

### 1. Why Direct Connectivity Does Not Scale

Two direct approaches have been considered, and both have a limitation:

- **Full Mesh** gives every pair a direct link, but needs N(N − 1) / 2 links, which grows far too quickly.
- **Bus** uses very few links, but all computers share one medium, which wastes bandwidth and leads to collisions and the need for MAC.

As the number of computers grows, a way is needed to connect them **without** a direct link between every pair and **without** forcing all of them to compete for one shared medium. The answer is **indirect connectivity**.

### 2. Indirect Connectivity

**Indirect connectivity** means that two computers are **not connected directly to each other**. Instead, they communicate **through one or more intermediate nodes or devices**.

Direct connectivity:

```
A ───────── B
```

Indirect connectivity:

```
A ───────── X ───────── B
```

Here A and B have no link between them. The information from A reaches B **via X**.

The key benefit is that a computer needs a connection only to the **intermediate point**, not to every other computer. This greatly reduces the requirement for direct links between every pair of computers.

### 3. Intermediate Devices

An **intermediate device** (or intermediate node) is a node placed between communicating computers that **connects them indirectly**.

- It connects computers that are not directly connected to one another.
- Information passes through it on its way from the source computer to the destination computer.
- Its presence replaces the need for a separate direct link between each pair of computers.

#### Additional Understanding

Common real-world intermediate devices include **switches**, **hubs**, and **routers**. The lecture-level idea is only that an intermediate node enables indirect connectivity.

### 4. Star Topology

In a **Star topology**, all computers are connected to a **central connecting point**, and communication between computers takes place **through** that central point.

```
       A
       |
B ──── X ──── C
       |
       D
```

Here X is the central connecting point. A, B, C, and D are not connected to each other directly. If A wishes to communicate with C, the information travels from A to X and then from X to C.

```
A ──► X ──► C
```

**Characteristics**

- **Indirect connectivity:** every communication passes through the central point.
- **Fewer links than Full Mesh:** each computer needs one link, to the central point.
- **Central role:** the central point is the key element of the structure.

**Link count (derived by counting, Additional Understanding):** For N computers around one central point, N links are needed. For N = 5, a Star needs 5 links, while a Full Mesh needs 10. For N = 100, a Star needs 100 links, while a Full Mesh needs 4,950.

### 5. Ring Topology

In a **Ring topology**, each computer is connected to its **neighbors**, so that the computers form a closed loop. Connectivity is **neighbor-to-neighbor**.

```
A ─── B
|     |
D ─── C
```

A larger ring:

```
      A ─── B
     /       \
    F         C
     \       /
      E ─── D
```

Information from one computer to a non-neighboring computer is passed **onward through other computers** in the ring. For example, from A to C in the first drawing, the information may pass through B (or through D).

```
A ──► B ──► C
```

**Characteristics**

- Each computer has a link to only its neighbors, not to all other computers.
- Communication between non-neighbors is **indirect**, passing through intermediate computers.

**Link count (derived by counting, Additional Understanding):** For N computers in a ring, N links are needed (one between each pair of neighbors).

### 6. Direct vs Indirect Connectivity

| Feature | Direct Connectivity | Indirect Connectivity |
|---|---|---|
| Connection between communicating computers | A link between the two computers themselves | Through one or more intermediate nodes |
| Diagram | `A ───── B` | `A ───── X ───── B` |
| Links required for N computers | Can be as many as N(N − 1) / 2 (Full Mesh) | Far fewer (for example, Star needs about N) |
| Intermediate nodes | None | Required |
| Scalability | Poor for many computers | Better; motivates scalable networks |
| Examples | Point-to-Point, Full Mesh | Star, Ring, Inter-Networks |

Diagrams side by side:

```
Direct (Full Mesh, N = 4)        Indirect (Star, N = 4)

A ─────── B                            A
│ ╲     ╱ │                            |
│   ╲ ╱   │                      B ─── X ─── C
│   ╱ ╲   │                            |
│ ╱     ╲ │                            D
D ─────── C

6 links                          4 links (plus the central point)
```

### 7. Star vs Full Mesh

| Feature | Star | Full Mesh |
|---|---|---|
| Type of connectivity | Indirect (through the central point) | Direct (every pair) |
| Central point | Yes | No |
| Links for N = 5 | 5 | 10 |
| Links for N = 100 | 100 | 4,950 |
| Scalability in links | Good (grows with N) | Poor (grows with N squared) |
| Path between two computers | Through the central point | Dedicated direct link |
| Main dependence | The central connecting point | Large number of links |

Link counts for Star are derived by counting and are given as Additional Understanding.

### 8. Inter-Networks

An **Inter-Network** is formed when **separate networks are connected to one another**. Each network may itself have a structure such as Bus, Star, or Ring. Interconnecting them allows computers in one network to communicate with computers in another.

The same principle of indirect connectivity applies: computers in different networks communicate **through intermediate nodes** that connect the networks.

### 9. Networks of Networks

The idea of an Inter-Network is often stated as **Networks of Networks**: instead of building one very large network in which every computer is connected in a single structure, many smaller networks are built and then connected.

```
+------------------+            +------------------+
|    Network 1     |            |    Network 2     |
|                  |            |                  |
|   A       B      |            |     D       E    |
|    \     /       |            |      \     /     |
|     \   /        |            |       \   /      |
|      [X1]--------+------------+-------[X2]       |
+---------|--------+            +--------|---------+
          |                              |
          |                              |
          |        +-----------------+   |
          +--------+    [X3]         +---+
                   |                 |
                   |    Network 3    |
                   |    F     G      |
                   +-----------------+
```

Here X1, X2, and X3 stand for **interconnecting nodes** that join the networks. Each network keeps its own internal structure, and the interconnecting nodes allow communication to cross from one network to another.

Why this is scalable:

- Each network remains of manageable size.
- Networks are joined by a limited number of connections rather than by a link between every pair of computers.
- New networks can be added by connecting them to the existing structure.

### 10. Internet

The **Internet** is the structural idea of **interconnected networks** taken to a very large scale: a global **Network of Networks**. It is not one single network with one single structure. It consists of many individual networks connected together so that computers in different networks can communicate.

```
Network Representation
        |
Direct connectivity (few systems)
        |
Scalability problem (many systems)
        |
Indirect connectivity (Star, Ring)
        |
Inter-Networks (Networks of Networks)
        |
Internet
```

This progression summarizes why the Internet is built from interconnected networks: direct connection of every pair of computers does not scale, so connectivity is arranged indirectly and in multiple interconnected networks.

### 11. Additional Understanding

The following points are general networking knowledge and are not drawn from the lecture.

- **Star in practice:** Modern wired LANs commonly use a star arrangement, with a central device (such as a switch) connecting all computers. Wireless LANs also follow a star-like pattern, with an access point as the central connecting point.
- **Ring in practice:** Ring structures appear in some historical LAN technologies and in some metropolitan and backbone networks, where a ring offers an alternative path if one link fails.
- **Interconnecting devices:** Networks are typically joined by **routers**, which forward information between networks based on destination addresses.
- **Hierarchy:** The Internet is organized as many independently operated networks (often called autonomous systems) that interconnect. Large networks are connected at various points, so the real structure is not a simple star or ring but a combination of many structures.
- **Hybrid topologies:** Practical networks often combine topologies, for example several stars connected to each other.

### 12. Cybersecurity Connection

- **Central point as a chokepoint:** In a Star, all traffic passes through the central point. This is useful for security because it is a natural place to **monitor, filter, and control** traffic. It is also a **single point of failure** and a high-value target: if it is compromised or disabled, the whole star is affected.
- **Ring dependency:** In a Ring, traffic passes through intermediate computers. A compromised or failed node on the path may disrupt or observe the traffic that passes through it.
- **Intermediate nodes in general:** Anything that carries traffic on behalf of others can observe or alter it. Intermediate devices must therefore be secured and trusted.
- **Inter-Networks and boundaries:** The points where networks connect are **trust boundaries**. Firewalls, access control, and monitoring are commonly placed at such points to limit the spread of an attack from one network into another.
- **Spread of attacks across networks:** In a Network of Networks, a compromise in one network may spread to others through the interconnections. Segmentation and careful control of interconnections reduce this risk.
- **Scale and exposure:** The Internet connects a very large number of networks, so any systems connected to it are potentially reachable from many other networks, which increases exposure.

### 13. Exam Answer

**Question:** Why does direct connectivity fail to scale? Explain indirect connectivity with reference to Star and Ring topologies, and describe how Inter-Networks lead to the idea of the Internet. (10 marks)

**Answer:**

**Limitation of direct connectivity.** In direct connectivity, computers are joined by links between themselves. In a **Full Mesh**, every pair of computers needs a link, so the number of links is N(N − 1) / 2, which grows very rapidly with N. A **Bus** uses few links but all computers share one medium, which leads to broadcast, collisions, the need for MAC, and inefficient use of bandwidth. Hence direct connectivity does not scale to a large number of computers.

**Indirect connectivity.** In **indirect connectivity**, two computers communicate **through intermediate nodes** instead of through a link between themselves.

```
Direct:     A ───────── B
Indirect:   A ───────── X ───────── B
```

Each computer needs a connection only to the intermediate point, which greatly reduces the number of links.

**Star topology.** All computers are connected to a **central connecting point** and communicate **through** it.

```
       A
       |
B ──── X ──── C
       |
       D
```

**Ring topology.** Each computer is connected to its **neighbors**, forming a closed loop. Communication with non-neighboring computers passes through intermediate computers.

```
A ─── B
|     |
D ─── C
```

**Inter-Networks and the Internet.** Separate networks can themselves be connected to each other, forming an **Inter-Network**, also described as a **Network of Networks**. Each network keeps its own structure and the networks are linked by interconnecting nodes.

```
[ Network 1 ] ──── [ Network 2 ] ──── [ Network 3 ]
```

The **Internet** is this idea at a very large scale: a global collection of interconnected networks.

**Conclusion.** Because direct connection of every pair of computers is inefficient, scalable connectivity uses intermediate nodes and interconnected networks, and this structural idea underlies the Internet.

### 14. Quick Revision

- Full Mesh: too many links. Bus: shared bandwidth and collisions. Neither scales well.
- **Indirect connectivity:** communication through **intermediate nodes**, not directly between the two computers.
- **Star:** central connecting point; communication goes through it.
- **Ring:** neighbor-to-neighbor connectivity in a closed loop.
- **Inter-Network:** separate networks connected together.
- **Networks of Networks:** many networks interconnected instead of one huge flat structure.
- **Internet:** interconnected networks at a global scale.
- Star links (derived): N for N computers. Full Mesh: N(N − 1) / 2.

### 15. Knowledge Check

1. What is indirect connectivity?
   *Answer: Communication between computers through intermediate nodes rather than through a direct link between them.*
2. In a Star topology, how does computer A communicate with computer C?
   *Answer: Through the central connecting point.*
3. How is connectivity arranged in a Ring?
   *Answer: Each computer is connected to its neighbors, forming a closed loop.*
4. What does "Networks of Networks" mean?
   *Answer: Separate networks that are interconnected to form a larger structure.*
5. How does the idea of the Internet relate to Inter-Networks?
   *Answer: The Internet is the global-scale form of interconnected networks.*
6. For 5 computers, how many links does a Star need compared with a Full Mesh?
   *Answer: Star 5 (derived by counting); Full Mesh 10.*

---

## Section B — Final Revision

### Core Concepts

| Concept | Key Idea |
|---|---|
| Network Representation | Systems drawn as nodes joined by links; shows structure (connectivity), not communication. |
| Point-to-Point | Two endpoints connected directly; in the simple model no addressing, routing, or protocol is needed; focus on the physical layer (coding, modulation). |
| Bus | All computers attached to one shared medium; few links but shared bandwidth. |
| Full Mesh | Every pair of computers directly connected; N(N − 1) / 2 links; inefficient in number of links. |
| Collision | Simultaneous transmissions overlap on a shared medium and the information is unusable. |
| MAC | Media Access Control; controls how computers access the shared medium. |
| Addressing | Identifies the intended receiver; necessary because the shared medium is broadcast. |
| Indirect Connectivity | Communication through intermediate nodes instead of direct links between all pairs. |
| Star | All computers connected to a central point; communication goes through it. |
| Ring | Each computer connected to its neighbors in a closed loop. |
| Inter-Network | Separate networks connected together. |
| Internet | Global-scale Network of Networks. |

### Important Formulas

**Full Mesh**

```
Links = N(N − 1) / 2
```

- **N** is the number of computers (nodes) in the network.
- (N − 1) is the number of other computers each computer must connect to.
- Division by 2 prevents each link from being counted twice (once from each end).

| N | Links |
|---|---|
| 2 | 1 |
| 3 | 3 |
| 4 | 6 |
| 5 | 10 |

**Derived counts (Additional Understanding)**

```
Star links = N
Ring links = N
```

### Important Comparisons

**Point-to-Point vs Multi-Computer**

| Feature | Point-to-Point | Multi-Computer |
|---|---|---|
| Endpoints | Two | Many |
| Intended receiver | Obvious | Needs addressing |
| Medium | Not shared | May be shared (Bus) |
| Collisions | Not a concern in the simple model | Possible on shared medium |
| Concern | Physical layer | Addressing, medium access, scalability |

**Bus vs Full Mesh**

| Feature | Bus | Full Mesh |
|---|---|---|
| Links | Few | N(N − 1) / 2 |
| Medium | Shared, broadcast | Dedicated per pair |
| Collisions and MAC | Yes | Not from sharing |
| Inefficiency | Bandwidth | Number of links |

**Direct vs Indirect Connectivity**

| Feature | Direct | Indirect |
|---|---|---|
| Path | Link between the two computers | Through intermediate nodes |
| Links for many computers | Very many (Full Mesh) | Far fewer |
| Scalability | Poor | Better |

**Star vs Full Mesh**

| Feature | Star | Full Mesh |
|---|---|---|
| Connectivity | Indirect, via central point | Direct, every pair |
| Links for N = 5 | 5 | 10 |
| Growth of links | Proportional to N | Proportional to N squared |
| Dependence | Central point | Many links |

### Exam Keywords

Terms that should appear in handwritten answers:

- Node, link, connectivity, topology
- Direct connectivity, indirect connectivity
- Point-to-Point, two endpoints
- Physical layer, coding, modulation
- Addressing, routing, protocol
- Bus topology, shared medium, broadcast
- Intended receiver
- Collision
- Media Access Control (MAC)
- Full Mesh topology, N(N − 1) / 2
- Scalability, scaling problem
- Intermediate node / intermediate device
- Star topology, central connecting point
- Ring topology, neighbor-to-neighbor
- Inter-Network, Networks of Networks
- Internet

### Common Exam Mistakes

1. **Confusing structure with communication.** A network diagram shows connectivity, not how data is exchanged.
2. **Over-generalizing Point-to-Point.** Stating that real point-to-point links never use protocols. The statement applies to the simplified model only.
3. **Forgetting why addressing is needed on a Bus.** The reason is the broadcast nature of the shared medium and the need to identify the intended receiver.
4. **Mixing up the inefficiencies.** Mesh is inefficient in the **number of links**; Bus is inefficient in **bandwidth**.
5. **Using the wrong Full Mesh formula.** The correct formula is N(N − 1) / 2, not N(N − 1) and not N squared.
6. **Defining a collision incorrectly.** A collision is caused by simultaneous transmissions on a shared medium, not by a faulty cable.
7. **Treating MAC as a protocol detail.** At this level, MAC is the idea of controlling access to a shared medium.
8. **Stating that Star has no intermediate node.** In a Star, communication always passes through the central connecting point.
9. **Describing the Internet as a single large network.** It is a Network of Networks.
10. **Omitting diagrams.** Handwritten answers on topologies should include a simple labeled diagram.

### Final Concept Map

```
Network Representation
        ↓
Direct Connectivity
        ↓
Point-to-Point
        ↓
Multiple Computers
        ↓
Bus / Full Mesh
        ↓
Scalability Problem
        ↓
Indirect Connectivity
        ↓
Star / Ring
        ↓
Inter-Networks
        ↓
Networks of Networks
        ↓
Internet
```

Expanded view of the branching points:

```
                    Multiple Computers
                     /              \
                    /                \
                 Bus               Full Mesh
        (shared medium)        (every pair linked)
        broadcast                N(N - 1) / 2 links
        addressing               inefficient in links
        collisions
        MAC
        inefficient in bandwidth
                    \                /
                     \              /
                  Scalability Problem
                          |
                  Indirect Connectivity
                     /          \
                  Star          Ring
           (central point)   (neighbors)
                     \          /
                      Inter-Networks
                             |
                    Networks of Networks
                             |
                         Internet
```
