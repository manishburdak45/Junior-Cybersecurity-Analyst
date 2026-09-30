# Computer Networks

##  Chapter 1: Introduction to Computer Networks

# Section A: Network Fundamentals

**Primary source:** Lecture_1a.pdf, "Computer Networks: Introduction", Prof. Neminath Hubballi, Department of Computer Science and Engineering, IIT Indore (NPTEL).

**Scope of this document:** Section A only.

1. What is a Network?
2. What Does a Network Do?
3. Why Study Networking?

Sections B to H of Chapter 1 are intentionally not covered here.

---

## Table of Contents

- [How to Read This Document](#how-to-read-this-document)
- [Topic 1: What is a Network?](#topic-1-what-is-a-network)
- [Topic 2: What Does a Network Do?](#topic-2-what-does-a-network-do)
- [Topic 3: Why Study Networking?](#topic-3-why-study-networking)
- [Section A: Final Revision](#section-a-final-revision)
- [Section A: Knowledge Check](#section-a-knowledge-check)
- [Answer Key / Expected Points](#answer-key--expected-points)

---

## How to Read This Document

Lecture content and general knowledge are kept strictly separate.

| Label | Meaning |
|---|---|
| **Lecture Content** | Taken directly from the lecture slides. Terminology is preserved exactly as the lecturer wrote it. |
| **Explanation** | A plain-language explanation of the lecture content. It adds no new facts. |
| **Additional Understanding** | Technical knowledge that is NOT in the lecture slides but helps to understand the concept. Never treat this as lecture content. |

> **Note on the source material:** The lecture slides for this section are short. The definition slide, the "What Network Does?" slides and the "Why Study Networking?" slides contain only a few words each, plus diagrams. Everything beyond those words is explanation or Additional Understanding, and is labelled accordingly.

> **Note on the files provided:** Only the six lecture PDFs were supplied. No ZIP or existing repository was found, so no existing README, notes or folder structure was used or changed.

---

# Topic 1: What is a Network?

## 1.1 Lecture Content

The lecture slide titled "1. What is Network ?" gives this definition:

> **A network is an interconnection of computer systems to transfer data between the applications running in these systems.**

The slide's diagram shows two computers joined by a single straight line.

```
[Computer]--------------------------------------[Computer]
```

## 1.2 The Definition Word by Word

| Phrase in the definition | What it means |
|---|---|
| **Interconnection** | The systems are linked to each other, so that a path exists between them. |
| **Computer systems** | The machines that take part. Each one is a system that runs applications. |
| **Transfer data** | Data moves from one system to another. This is the actual job of the network. |
| **Applications** | The programs that produce and use the data. They are the real endpoints of communication. |
| **Running in these systems** | The applications live inside the computer systems, one or more per system. |

### Interconnection

**What is it?** A link that joins systems so that they can reach each other.

**Why is it needed?** Two systems with no link between them cannot exchange anything.

**How does it work at a high level?** Systems are connected by communication links. The lecture diagram shows this as a line between two computers.

**Why does it matter in cybersecurity?** Every interconnection is a path along which data can travel. Anything that can travel along a path can also be observed, blocked or abused.

### Computer systems

**What is it?** The machines being interconnected. The lecture uses the phrase "computer systems" and draws them as desktop computers.

**Why is it needed?** They are the places where data is created and consumed.

**Why does it matter in cybersecurity?** Each computer system is a potential source or destination of traffic, so it is also a potential target.

### Transfer of data

**What is it?** Moving data from one system to another.

**Why is it needed?** It is the reason networks exist. The next topic in the lecture, "What Network Does?", is about this.

### Applications

**What is it?** A program running on a computer system.

**Why does it matter?** The definition does not say "data between computers". It says "data between the **applications** running in these systems". This wording is deliberate and is explained in section 1.5.

## 1.3 What Does "Interconnection" Mean?

**Explanation:** Interconnection means the systems are joined so that data can flow between them. The lecture diagram represents this with a line.

**Additional Understanding:**

- The line represents a **communication link**. A link can be **wired** (for example a cable) or **wireless** (for example radio signals).
- Connection alone is not the purpose of a network. A cable between two computers that never carries any data achieves nothing. The purpose stated in the definition is "to transfer data between the applications".

> Interconnection is the **means**. Data transfer between applications is the **goal**.

## 1.4 What Are Computer Systems?

**Lecture Content:** The lecture uses the term "computer systems" and illustrates it with desktop computer icons.

**Explanation:** At an introductory level, a computer system is any machine that can run applications and can take part in communication.

**Additional Understanding:** The following examples are **not from the lecture definition slide**. They are added to make the term concrete.

| Example (additional) | Typical role |
|---|---|
| Laptop | Runs a web browser or other user applications |
| Desktop | Same as above |
| Server | Runs applications that serve other systems |
| Smartphone | Runs apps that use network services |

The lecture's later slide on "Why Study Networking ?" shows that many non-traditional devices also connect to networks. That is covered in Topic 3.

## 1.5 What is Data Transfer?

| Term | Meaning in a network |
|---|---|
| **Data** | The information being moved. In the lecture's "What Network Does?" slide, data is drawn as the bits `1 1 0`. |
| **Source** | The system (and application) where the data starts. |
| **Destination** | The system (and application) where the data must arrive. |
| **Communication** | The exchange of data between source and destination. |
| **Data movement between systems** | Data leaves one system, passes over the links, and appears in another system. |

**Explanation:** Data transfer is complete only when the destination holds the same data that the source sent. The lecture's diagram for this (covered in Topic 2) shows exactly that idea.

## 1.6 Application-to-Application Communication

**This is the most important idea in Topic 1.**

The lecture definition says the data is transferred "between the applications running in these systems". A network is therefore **not** simply about connecting physical computers.

```
   Application A                          Application B
   (runs on System 1)                     (runs on System 2)
         |                                      ^
         |                                      |
         v                                      |
   +-------------------------------------------------+
   |                    NETWORK                      |
   +-------------------------------------------------+
```

**Explanation:**

- A computer is only a host for applications.
- Application A on one system produces data. Application B on another system needs that data.
- The network carries the data from A to B.
- Two computers that are physically connected but run no communicating applications are not doing anything useful as a network.

**Why is it needed?** Users never interact with "a computer" in the abstract. They interact with applications. The network exists to serve those applications.

**Why does it matter in cybersecurity?** Attacks and defences usually target specific applications and services. To understand which traffic belongs to which application, you must first think in terms of application-to-application communication.

## 1.7 Basic Network Communication Model

### Simple model

```
Sender -----> Network -----> Receiver
```

| Component | Role |
|---|---|
| **Sender** | The system that originates the data |
| **Network** | The interconnection that carries the data |
| **Receiver** | The system that is meant to get the data |

### Application-level model

```
Application --> System --> Network --> System --> Application
   (A)         (Host 1)                (Host 2)       (B)
```

| Component | Role |
|---|---|
| **Application (A)** | Creates the data to be sent |
| **System (Host 1)** | The computer system on which Application A runs |
| **Network** | Carries the data between the two systems |
| **System (Host 2)** | The computer system on which Application B runs |
| **Application (B)** | Receives and uses the data |

**Note:** The diagram above is an explanatory model built from the lecture definition. The lecture slide itself shows only two computers and a line.

## 1.8 Network Components (Introductory)

**Additional Understanding:** The lecture definition does not list network components. The three groups below are standard introductory terms.

| Component | Introductory meaning |
|---|---|
| **End systems / hosts** | The computer systems at the edges that run the applications (the sender and receiver). |
| **Communication links** | The connections (wired or wireless) over which data travels. |
| **Network devices** | Equipment placed between end systems that helps data travel from source to destination. |

Specific devices such as switches and routers, and ideas such as addressing and routing, belong to later topics and are deliberately not explained here.

## 1.9 Real-World Examples

All examples below are **Additional Understanding** unless stated.

| Example | Application on one side | Application on the other side |
|---|---|---|
| Laptop communicating with a web server | Web browser | Web server software |
| PC-to-PC communication | An application on PC 1 | The matching application on PC 2 |
| Client-server communication | Client application | Server application |
| Mobile device using Internet services | A mobile app | A service running on a remote server |

In every case the same pattern holds: **an application on one system exchanges data with an application on another system through a network.**

## 1.10 Network vs Internet

| | Network | Internet |
|---|---|---|
| **Basic idea** | A collection of interconnected systems | A very large interconnection of networks, a network of networks |
| **Size** | Can be as small as two computers | Very large |
| **Relationship** | Many networks exist | The Internet is built by interconnecting networks |

**Lecture pointer:** A later lecture slide (Lecture 2b, "Inter-Networks: Networks of Networks") shows several small networks joined together and labelled as equal to "Internet". That material belongs to a later section of Chapter 1 and is only previewed here.

**Key point:** Every Internet is made of networks, but a network is not necessarily the Internet. A small office network is a network even if it is not connected to anything else.

## 1.11 Basic Network Diagrams

### Minimal network (matches the lecture slide)

```
+------------+                          +------------+
| Computer A |--------------------------| Computer B |
+------------+                          +------------+
```

### Same network, with the network shown as a cloud

```
+------------+        ( - - - - - - - )        +------------+
| Computer A |------->(    Network    )------->| Computer B |
+------------+        ( - - - - - - - )        +------------+
```

### A more realistic high-level model (Additional Understanding)

```
+---------+    +--------+                +--------+    +---------+
| App A   |    | System |                | System |    | App B   |
| (client)|--->| Host 1 |--> Links and ->| Host 2 |--->| (server)|
+---------+    +--------+  network devs  +--------+    +---------+
```

## 1.12 Cybersecurity Connection (Additional Understanding)

A network is, by definition, a path along which data moves from a source to a destination. Security work is largely about that movement.

| Networking idea | Why it matters for security |
|---|---|
| Network traffic | Traffic is the raw material that security tools examine. |
| Source and destination | Two basic questions in any investigation are "who sent this?" and "where was it going?" |
| Communication monitoring | Watching what communicates with what can reveal abnormal behaviour. |
| Packet analysis | Inspecting the data units that travel on the network. |
| Network security | Protecting the links, devices and data on the network. |
| IDS/IPS | Systems that watch traffic for suspicious activity (and, for IPS, act on it). |
| Firewalls | Devices or software that allow or block traffic according to rules. |
| Network reconnaissance | Attackers often begin by mapping a network before attacking it. |

> You cannot defend or investigate a network without first understanding what normal communication between applications looks like.

These topics are named only to show the connection. They are not taught here.

## 1.13 Common Misunderstandings

| Misunderstanding | Correction |
|---|---|
| A network is just a cable. | The cable (link) is one part. The lecture defines a network as an interconnection of systems **to transfer data between applications**. |
| A network is the same as the Internet. | The Internet is a network of networks. A network can be small and private. |
| Connecting computers is the final purpose. | Connection is the means. Data exchange between applications is the goal. |
| Computers talk to computers. | According to the lecture definition, the communicating parties are the **applications** running on the systems. |

## 1.14 Exam Answer: What is a Computer Network?

**Definition:**

> A network is an interconnection of computer systems to transfer data between the applications running in these systems.

**Supporting points:**

1. Computer systems are interconnected through communication links.
2. The purpose of the interconnection is to transfer data.
3. The communicating parties are the applications running on the systems, not merely the machines.
4. Data starts at a source and must arrive at a destination.
5. The Internet is a very large interconnection of such networks.

**Diagram:**

```
Application A            Application B
     |                        ^
     v                        |
 [System 1] ---- Network ---- [System 2]
```

## 1.15 Interview / Viva Questions

<details>
<summary><b>What is a computer network?</b></summary>

An interconnection of computer systems to transfer data between the applications running in these systems.
</details>

<details>
<summary><b>Why do we need a network?</b></summary>

So that applications running on different systems can exchange data. Without a network, each system would be isolated.
</details>

<details>
<summary><b>What is application-to-application communication?</b></summary>

It means that the real endpoints of communication are the applications, not the machines. For example, a web browser on one system exchanges data with server software on another system. The network carries the data between them.
</details>

<details>
<summary><b>What is the difference between a network and the Internet?</b></summary>

A network is a collection of interconnected systems. The Internet is a very large interconnection of networks, a network of networks.
</details>

<details>
<summary><b>What are the basic components of a network?</b></summary>

At an introductory level: end systems (hosts), communication links, and network devices. (This answer is Additional Understanding; it is not on the definition slide.)
</details>

## 1.16 Quick Revision: Topic 1

**Key definition:** A network is an interconnection of computer systems to transfer data between the applications running in these systems.

**Key terms:** interconnection, computer systems, data transfer, applications, source, destination, link, host.

**Five important points:**

1. A network has two essential ingredients: interconnection and data transfer.
2. The endpoints of communication are applications.
3. Connecting machines is the means; data exchange is the goal.
4. A network is not just a cable.
5. The Internet is a network of networks, not a synonym for every network.

**One-line memory concept:** *Connected systems, communicating applications.*

---

# Topic 2: What Does a Network Do?

## 2.1 Lecture Content

Two consecutive lecture slides are titled "2. What the Network Does ?" and "2. What Network Does ?". Both show the same picture:

- A computer on the left with three data cells containing **1 1 0**.
- A computer on the right with three **empty** data cells.

```
   [Computer]                              [Computer]
   +---+---+---+                           +---+---+---+
   | 1 | 1 | 0 |                           |   |   |   |
   +---+---+---+                           +---+---+---+
```

The slides contain no other text.

**Explanation:** The picture poses a question. The left system holds data. The right system does not. What the network does is make the data on the left appear on the right. In the PDF only this starting state is visible; the final state, with the right-hand cells filled to `1 1 0`, is the natural completion of the idea (this is an interpretation).

**Main concept:** A network enables communication and data transfer between systems and applications.

## 2.2 Core Concepts

| Concept | Meaning |
|---|---|
| **Basic purpose** | To move data from one system to another. |
| **Communication** | The exchange of data between two parties. |
| **Data transfer** | Copying data from where it exists to where it is needed. |
| **Sender** | The party that has the data and transmits it. |
| **Receiver** | The party that gets the data. |
| **Source** | The origin of the data (the location of the sender). |
| **Destination** | The intended final location of the data (the location of the receiver). |
| **Communication path** | The route through the network by which data travels from source to destination. |
| **Application communication** | The exchange of data between applications, as in Topic 1. |

### Sender and Receiver vs Source and Destination

These pairs are closely related. In this course-level view:

- **Sender / Receiver** emphasise the **parties** taking part.
- **Source / Destination** emphasise the **locations** in the flow of data.

Both pairs are useful. For example, "source" and "destination" are the words used when describing where a piece of traffic came from and where it is going.

### Communication path (Additional Understanding)

The lecture slide draws no path. In general, data does not teleport: it travels along one or more links. The exact way that path is chosen is a later topic.

### Two-way communication (Additional Understanding)

The lecture picture shows data moving one way (left to right). In practice, communication is usually two-way. The system that received data often sends something back. The request and response example below shows this.

## 2.3 Communication Models

### Basic flow

```
Sender -----> Network -----> Receiver
```

### Application-level flow

```
Application A
     |
     v
  Network
     |
     v
Application B
```

### Explanation of every component

| Component | Explanation |
|---|---|
| **Sender / Application A** | Creates the data and hands it to the network. |
| **Network** | Carries the data. The sender and receiver do not need a private wire between them for each message. |
| **Receiver / Application B** | Accepts the data and uses it. |
| **Data (for example 110)** | What is being moved. Must arrive at the destination exactly as the source sent it. |

### Two-way view

```
Application A  <-------  Network  ------->  Application B
   (sends request)                        (sends response)
```

## 2.4 Example: Request and Response (Additional Understanding)

A practical way to see two-way communication is the request and response pattern.

| Step | What happens |
|---|---|
| 1 | One application sends a **request** (asking for something). |
| 2 | The network carries the request to the other application. |
| 3 | The other application prepares a **response**. |
| 4 | The network carries the response back. |

## 2.5 Web Browsing Example (High Level)

This example uses web browsing only to illustrate the model. The lecture lists "Web browsing" as a reason to study networking. The detailed protocols are not covered here.

```
Browser
   |
   v
Request
   |
   v
Network
   |
   v
Web Server
   |
   v
Response
   |
   v
Browser
```

**Explanation:**

1. The user types an address or clicks a link. The **browser** (an application) creates a **request**.
2. The request travels across the **network**.
3. The **web server** (another application, on another system) receives it.
4. The server prepares a **response** (for example a web page).
5. The response travels back across the network to the browser, which displays it.

**Additional Understanding:** The exact rules that browsers and servers use to speak to each other, and the rules that move data across the network, are protocols (HTTP, TCP, IP and others). They are studied later in the course and are not explained here.

## 2.6 Cybersecurity Connection (Additional Understanding)

The model *source -> network -> destination* is also the basic model of security monitoring.

```
Source ----> Traffic ----> Network ----> Destination
```

Questions a security analyst may ask:

| Question | Connection to the model |
|---|---|
| **Who is communicating?** | Identify the source and destination. |
| **Where is the traffic going?** | Follow the path to the destination. |
| **What communication is taking place?** | Look at the nature of the data and the application involved. |
| **Is the communication expected?** | Compare it with what is normal for that source and destination. |

Unexpected communication (for example a system contacting a destination it never normally contacts) may be a sign of a problem. Detailed methods are outside this section.

## 2.7 Exam Answer: What Does a Computer Network Do?

**Answer:**

> A computer network enables communication and data transfer between systems and applications. It carries data from a source (sender) across the interconnection to a destination (receiver), so that the application at the destination receives the data that the application at the source sent.

**Key points:**

1. Takes data from source to destination.
2. Serves applications on different systems.
3. Supports two-way communication (for example request and response).
4. Works through an interconnection of links and devices.

**Diagram:**

```
Sender -----> Network -----> Receiver
  110                          110
```

## 2.8 Short-Answer Version

A network transfers data between applications running on different systems, from a source to a destination.

## 2.9 Interview / Viva Questions

<details>
<summary><b>What does a network do?</b></summary>

It transfers data between systems, and more specifically between the applications running on those systems.
</details>

<details>
<summary><b>What do the slides with "1 1 0" show?</b></summary>

One system holds the data 1 1 0 and the other holds empty cells. The idea is that the network moves the data from the first system to the second.
</details>

<details>
<summary><b>What is the difference between source and destination?</b></summary>

The source is where the data originates; the destination is where it is meant to arrive.
</details>

<details>
<summary><b>Explain request and response using web browsing.</b></summary>

The browser sends a request across the network to a web server. The server sends a response back across the network. The browser shows it. (Additional Understanding.)
</details>

<details>
<summary><b>Why is the source-network-destination model relevant to security?</b></summary>

Security monitoring asks who is communicating, where traffic is going, what is being communicated, and whether it is expected. These are all questions about sources, destinations and the path between them. (Additional Understanding.)
</details>

## 2.10 Quick Revision: Topic 2

- **Key idea:** A network enables communication and data transfer between systems and applications.
- **Key terms:** sender, receiver, source, destination, communication path, request, response.
- **Lecture picture:** data `1 1 0` on one system, empty cells on the other.
- **Remember:** communication is often two-way.
- **One-line memory concept:** *A network makes the data here appear over there.*

---

# Topic 3: Why Study Networking?

## 3.1 Lecture Content

The slide titled "3. Why Study Networking ?" states "Many reasons to study" and lists:

- Web browsing
- Email
- Video conferencing
- Audio call (VoIP)
- Remote desktop connection
- Many more

A second slide with the same title shows pictures of many network-connected devices (see section 3.7).

**Explanation:** The lecture's argument is that networking sits underneath a very wide range of everyday activities and devices.

## 3.2 Web Browsing

| Question | Answer |
|---|---|
| **What is it?** | Viewing web pages and content using a browser. |
| **Why does it need networking?** | The page is stored on another system. It must be transferred to the user's system. |
| **Basic communication model** | Browser (application) -> network -> web server (application) -> network -> browser |
| **Real-world example** | Opening a news page in a browser. |
| **Cybersecurity relevance** | Web traffic is among the most common traffic types, and a frequent target and carrier of attacks. |

```
Browser ---- request ---> Network ---> Web Server
Browser <--- response --- Network <--- Web Server
```

Only the introductory model is given here. Protocol details come later.

## 3.3 Email

| Question | Answer |
|---|---|
| **What is it?** | Sending and receiving written messages electronically. |
| **Why does it need networking?** | The sender and the recipient are on different systems, often far apart. |
| **Basic communication model** | Sender -> network -> mail infrastructure/server -> network -> receiver |
| **Real-world example** | Sending a message to a colleague. |
| **Cybersecurity relevance** | Email is a common route for attacks such as phishing. |

```
Sender ---> Network ---> Mail Server ---> Network ---> Receiver
```

**Additional Understanding:** A mail server typically holds messages until the recipient retrieves them, so the sender and receiver do not need to be online at the same moment. The rules for this are protocols that are studied later.

## 3.4 Video Conferencing

| Question | Answer |
|---|---|
| **What is it?** | A live meeting in which participants see and hear each other. |
| **Why does it need networking?** | Audio and video must be sent between participants as it is produced. |
| **Basic communication model** | Each participant sends audio and video to the others and receives theirs. |
| **Real-world example** | An online class or team meeting. |
| **Cybersecurity relevance** | The content is often private; it must be protected from interception. |

```
Participant A  <== audio + video ==>  Network  <== audio + video ==>  Participant B
```

Key ideas:

- **Audio communication** and **video communication** both use the network.
- The exchange is **continuous**: data keeps flowing for the whole session, in both directions.
- The session is fully **network dependent**: if the network fails or slows, the meeting suffers.

**Additional Understanding:** Audio and video are converted into digital data before being sent. The methods used (codecs and protocols) are outside this section.

## 3.5 Audio Call (VoIP)

**Additional Understanding on the name:** VoIP stands for **Voice over Internet Protocol**. The lecture writes only "Audio call (VoIP)"; the expansion is added here.

| Question | Answer |
|---|---|
| **What is it?** | Making voice calls over a data network. |
| **Why does it need networking?** | The voice must be carried to the other party as data. |
| **Basic communication model** | Voice -> digital data -> network -> digital data -> voice |
| **Real-world example** | A voice call made through an app on a smartphone. |
| **Cybersecurity relevance** | Calls can be eavesdropped on if not protected; voice services can also be abused. |

```
Voice
  |
  v
Digital data
  |
  v
Network
  |
  v
Digital data
  |
  v
Voice
```

**Additional Understanding:** The call needs to feel like a live conversation, so voice data must flow steadily in both directions. The signalling and media protocols used for this are a later topic.

## 3.6 Remote Desktop Connection

| Question | Answer |
|---|---|
| **What is it?** | Using a computer that is somewhere else, as if sitting at it. |
| **Why does it need networking?** | The screen, keyboard and mouse activity must travel between the user's computer and the remote one. |
| **Basic communication model** | Local computer -> network -> remote computer, with results coming back. |
| **Real-world example** | An IT administrator working on a server in another building. |
| **Cybersecurity relevance** | Remote access is a high-value target; exposed remote access services are frequently attacked. |

```
Local Computer
      |
      v
   Network
      |
      v
Remote Computer
```

Without network connectivity, the local computer has no way to reach the remote one at all.

**Additional Understanding:** Technologies for this include RDP, SSH and VNC. They are not explained here.

## 3.7 "Many More": Connected Devices (from the Lecture Slide)

The second "Why Study Networking ?" slide shows the following devices, exactly as labelled in the lecture:

| Device as labelled in the lecture | Short description (explanation, not lecture text) |
|---|---|
| Amazon Echo | A voice-controlled smart speaker |
| Internet refrigerator | A refrigerator with network connectivity |
| IP picture frame | A digital picture frame that receives pictures over a network |
| Pacemaker & Monitor | A medical implant together with a monitoring device |
| Tweet-a-watt: monitor energy use | A device that monitors energy use and reports it over a network |
| Web-enabled toaster + weather forecaster | A toaster connected to the web, paired with weather information |
| Internet phones | Telephones that work over a network |
| Slingbox: remote control cable TV | A device for controlling cable TV remotely |
| Security Camera | A camera whose video can be accessed over a network |
| sensorized, bed mattress | A mattress fitted with sensors |
| AR devices | Augmented reality devices |
| Fitbit | A wearable fitness tracker |
| Gaming devices | Game consoles and controllers |
| cars | Vehicles with network connectivity |
| scooters | Scooters with network connectivity |
| bikes | Bicycles with network connectivity |

**Broader concept:** Networking is **not limited to traditional computers**. Everyday objects, medical devices, vehicles and entertainment devices all communicate over networks.

**Additional Understanding:**

- Each connected device is another source or destination of data.
- Many such devices are small and have limited protection, which widens the set of things that must be secured.
- The pacemaker example shows why network security can matter for safety as well as privacy.

## 3.8 Cybersecurity Connection (Additional Understanding)

Networking is the foundation for cybersecurity because security events almost always involve data moving between systems.

| Networking knowledge | Security use |
|---|---|
| Understanding normal communication | Detecting suspicious communication |
| Network monitoring | Seeing what is happening on the network |
| Traffic analysis | Finding patterns in traffic |
| Packet analysis | Examining individual data units in detail |
| Reconnaissance | Recognising how attackers learn about a network |
| Firewalling | Deciding which communication to allow or block |
| IDS/IPS | Automated detection and prevention |
| Network attack analysis | Reconstructing how an attack travelled across the network |

The wider the set of connected things (Topic 3.7), the larger the area that must be understood and protected.

## 3.9 Quick Revision: Topic 3

- **Lecture list:** Web browsing, Email, Video conferencing, Audio call (VoIP), Remote desktop connection, Many more.
- **Lecture devices slide:** shows many non-computer devices connected to networks.
- **Common pattern:** in every example, an application on one system exchanges data with an application or service on another.
- **Security link:** every additional networked application or device is more traffic to understand and protect.
- **One-line memory concept:** *If it communicates, it needs a network.*

---

# Section A: Final Revision

## 1. Core definition

> A network is an interconnection of computer systems to transfer data between the applications running in these systems.

## 2. Core concepts

- Interconnection is the means; application-to-application data transfer is the goal.
- A network moves data from a source to a destination.
- Communication is often two-way (request and response).
- The Internet is a network of networks.

## 3. Important terminology

| Term | One-line meaning |
|---|---|
| Interconnection | Systems linked to each other |
| Computer system | A machine that runs applications and takes part in communication |
| Application | A program that produces or uses the data |
| Data transfer | Moving data from one system to another |
| Sender / Receiver | The parties that transmit and get data |
| Source / Destination | Where the data starts and where it must arrive |
| Communication path | The route data takes through the network |
| Request / Response | The two halves of a typical two-way exchange |

## 4. Network communication model

```
Application --> System --> Network --> System --> Application
```

## 5. Major applications (from the lecture)

Web browsing, Email, Video conferencing, Audio call (VoIP), Remote desktop connection, and many more, including a wide range of connected devices.

## 6. Cybersecurity relevance

Security depends on understanding sources, destinations, traffic and the applications behind them. Monitoring, packet analysis, firewalls, IDS/IPS and reconnaissance all build on basic networking knowledge.

## 7. Important exam points

1. Write the lecture definition exactly.
2. Explain why the definition says "applications" rather than "computers".
3. Draw the sender-network-receiver diagram.
4. Give the lecture's list of reasons to study networking.
5. Distinguish network from Internet.

## 8. Interview / viva questions (summary)

What is a network? Why do we need one? What is application-to-application communication? Network versus Internet? What are the basic components? What does a network do? Why study networking?

## 9. Common misconceptions

- A network is just a cable.
- A network and the Internet are the same thing.
- Connecting computers is the purpose of a network.
- Only traditional computers use networks.

---

# Section A: Knowledge Check

Attempt these without looking at the notes. Answers are at the end.

### Basic Questions

1. State the lecture's definition of a network.
2. What does the word "interconnection" mean?
3. Name the five reasons to study networking that are listed explicitly on the lecture slide (before "Many more").
4. What data pattern appears on the sending computer in the "What Network Does?" slide?
5. What is a destination?

### Conceptual Questions

1. Why does the definition say "applications" rather than just "computers"?
2. Why is connecting computers not the final purpose of a network?
3. Explain the difference between a network and the Internet.
4. Describe the request and response pattern using web browsing.
5. Why does video conferencing depend so strongly on the network?

### Interview / Viva Questions

1. Explain application-to-application communication in your own words.
2. What are the basic components of a network at an introductory level?
3. Give three examples from the lecture of non-traditional devices that connect to networks.
4. Why might a pacemaker's network connection be a security concern?
5. How does the source-network-destination model help a security analyst?

### Exam Questions

1. Define a computer network, and explain the definition word by word. (Give a diagram.)
2. Explain what a computer network does, using the sender, receiver, source and destination.
3. Explain why we study networking. Describe any three applications from the lecture.
4. Explain how a voice call over a network works at a high level.
5. Write short notes on: (a) Remote desktop connection, (b) Email.

---

## Answer Key / Expected Points

### Basic Questions

1. An interconnection of computer systems to transfer data between the applications running in these systems.
2. Systems being linked to each other so that data can travel between them.
3. Web browsing, Email, Video conferencing, Audio call (VoIP), Remote desktop connection.
4. `1 1 0`.
5. The location where the data is meant to arrive.

### Conceptual Questions

1. The real endpoints of communication are applications. A computer is the place where applications run. The network exists to serve those applications.
2. Connection is the means. Without data exchange between applications, a connection achieves nothing.
3. A network is a collection of interconnected systems; the Internet is a very large interconnection of networks (a network of networks).
4. The browser sends a request across the network to a web server. The server sends a response back across the network to the browser. (Additional Understanding.)
5. Audio and video must be sent continuously in both directions. A failing or slow network directly affects the meeting.

### Interview / Viva Questions

1. Applications on different systems exchange data through the network. The machines are hosts; the applications are the true communicating parties.
2. End systems (hosts), communication links, network devices. (Additional Understanding.)
3. Any three of: Amazon Echo, Internet refrigerator, IP picture frame, Pacemaker & Monitor, Tweet-a-watt, Web-enabled toaster + weather forecaster, Internet phones, Slingbox, Security Camera, sensorized bed mattress, AR devices, Fitbit, Gaming devices, cars, scooters, bikes.
4. It is a medical device. If its network communication were intercepted or manipulated, patient safety and privacy could be affected. (Additional Understanding.)
5. It frames the key questions: who is communicating, where is the traffic going, what is being communicated, and is it expected. (Additional Understanding.)

### Exam Questions (expected points)

1. Lecture definition; interconnection, computer systems, transfer of data, applications, application-to-application communication explained; diagram of Application A - Network - Application B.
2. Enables communication and data transfer between systems and applications; sender and receiver; source and destination; data moves across the network so that the destination holds the same data the source sent; diagram showing `110` at both ends.
3. The lecture's list; any three described with: what it is, why it needs networking, basic model; mention of connected devices and "many more".
4. Voice is converted to digital data, carried across the network, converted back to voice at the other end; continuous two-way flow. (Explanation of VoIP expansion is Additional Understanding.)
5. (a) Using a remote computer from a local one; needs a network to carry the session between the two. (b) Sender - network - mail infrastructure/server - receiver; the server sits between sender and receiver.

---

*End of Section A. Section B (How Computer Networks Look Like?) is not included in this document.*
