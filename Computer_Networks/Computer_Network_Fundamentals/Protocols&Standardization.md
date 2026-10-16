# Section D — Protocols & Standardization

Study notes on protocols, standardization, and the OSI Reference Model, prepared for MCQ-based assessment.

**Labelling convention used in this document**

- Unlabelled content is derived from Lecture 1b.
- Content marked `Additional Understanding` goes beyond the lecture and is included only to aid comprehension.

---

## 1. How to Study Networks?

### Lecture Approach

The lecture approaches the study of networks through **protocols**. Instead of starting with individual devices or technologies, it asks what rules the communicating parties follow. Understanding the rules explains how communication takes place.

### Human-Interaction Analogy

The lecture compares network communication with a simple human interaction: one person greets another, and the other responds. The exchange works because both people follow a shared, implicit set of conventions.

```text
Human Interaction

Person A  -------- "Hi" -------->  Person B
Person A  <------- "Hi" ---------  Person B

Network Communication

Entity A  -------- Network ------> Entity B
              Protocol Rules
```

### Key Ideas

- Communication happens between **entities**.
- The entities follow **rules** that govern the communication.
- In networks, these rules are called **protocols**.

### Limits of the Analogy

The analogy is an aid to understanding. Human conversation and network protocols are **not identical**: human conventions are informal and flexible, whereas network protocols are defined rules followed by communicating entities.

### Additional Understanding

Studying a network protocol-by-protocol lets a student ask the same questions each time: who is communicating, what rules do they follow, and what does each side expect from the other.

---

## 2. What Is a Protocol?

### Definition

> **A protocol is a set of rules governing communication between communicating entities.**

### Plain-English Explanation

A protocol is an agreement. It tells each side how to behave so that the other side can understand and respond correctly.

### Why Communication Requires Agreed Rules

- Both entities must interpret an exchange in the same way.
- Without agreed rules, one entity cannot reliably understand what the other is doing.
- Agreed rules make communication predictable.

### Protocol Rules vs Implementation

| Aspect | Meaning |
|---|---|
| Protocol | The **rules** that govern communication |
| Implementation | The **software or hardware** that follows those rules |

The protocol is the specification of behavior. Software or hardware carries it out. The two should not be confused.

### Communication Diagram

```text
+----------+                                +----------+
| Entity A |  ---- message following ---->  | Entity B |
|          |  <--- the agreed rules ------  |          |
+----------+                                +----------+
          Both sides follow the same protocol
```

### Practical Example

Two people greet each other: one says "Hi" and the other replies "Hi". Both know the convention for starting an exchange. In a network, two communicating entities likewise follow agreed rules for their exchange.

### Exam-Oriented Points

- Protocol = set of rules governing communication between communicating entities.
- A protocol defines rules; it is not itself the software or hardware.
- Communication between entities needs agreed rules.
- The lecture studies networks through protocols.

---

## 3. Need for Standardization

### Different Networking Systems

Networking involves many systems and technologies. Entities built around different systems cannot be assumed to follow the same rules.

### The Communication and Compatibility Problem

If each system uses its own rules, entities from different systems may be unable to communicate with each other.

### Purpose of Standardization

Standardization establishes **common rules and specifications** so that systems can follow the same agreed behavior.

### Standardization and Interoperability

When systems follow common standards, they can work together. This ability to work together is **interoperability**.

```text
Different Networking Systems
             |
             v
      Need for Common Rules
             |
             v
       Standardization
             |
             v
  Better Interoperability
```

### Additional Understanding

- A common standard reduces the need for every pair of systems to agree on separate rules.
- A standard is a published agreement, not a product. Different products can follow the same standard.

---

## 4. Standardization of Network Functionalities

### Historical Information from Lecture 1b

| Year | Event (as given in the lecture) |
|---|---|
| 1977 | ISO started the standardization process |
| 1984 | The first standard of layering architecture was released |

### Timeline

```text
1977
  |
  v
ISO started standardization
  |
  v
1984
  |
  v
First standard of layering architecture
  |
  v
OSI Reference Model
```

### Relationship Between Standardization, Layered Architecture, and the OSI Reference Model

- **Standardization** is the process that ISO started in 1977.
- **Layered architecture** is the structure for which the first standard was released in 1984, according to the lecture.
- The **OSI Reference Model** is the layered architecture presented next in the lecture (see Section 5).

### Additional Understanding — Why a Layered Architecture Is Useful

The following points are general explanations and are not stated in the lecture.

- Layering divides the overall communication task into smaller parts.
- Each layer has a defined role, which makes the system easier to describe and study.
- A common layered structure gives everyone the same vocabulary for discussing networks.

---

## 5. OSI Reference Model

**OSI — Open Systems Interconnection**

The OSI Reference Model is a layered architecture with **seven layers**.

### 5.1 Seven Layers

Layers as presented in the lecture, in top-to-bottom order:

| Layer Number | Layer Name |
|---:|---|
| 7 | Application |
| 6 | Presentation |
| 5 | Session |
| 4 | Transport |
| 3 | Network |
| 2 | Data Link |
| 1 | Physical |

```text
+----------------------+
| 7. Application       |
+----------------------+
| 6. Presentation      |
+----------------------+
| 5. Session           |
+----------------------+
| 4. Transport         |
+----------------------+
| 3. Network           |
+----------------------+
| 2. Data Link         |
+----------------------+
| 1. Physical          |
+----------------------+
```

The lecture slide provides the seven layer names. It does not give a detailed explanation of each layer's functionality.

### 5.2 Layer Numbers and Ordering

**Top to bottom (7 to 1):**
Application, Presentation, Session, Transport, Network, Data Link, Physical.

**Bottom to top (1 to 7):**
Physical, Data Link, Network, Transport, Session, Presentation, Application.

**Numbering and key positions**

- Numbering starts at **1 for the bottom layer** and rises to **7 for the top layer**.
- The **top layer** is layer 7 — **Application**.
- The **bottom layer** is layer 1 — **Physical**.

**Why layer identification matters in MCQs**

- Questions often give a layer number and ask for the name, or the reverse.
- Reversing the order is a common error: Application is 7, not 1.
- Options often list the correct names in a wrong order, so the sequence must be memorized exactly.

### 5.3 Additional Understanding — Basic Functions of OSI Layers

This section is **additional knowledge**. It is not direct lecture content.

| Layer | Basic Function |
|---|---|
| Application | Provides network services to applications |
| Presentation | Data representation, formatting, and related transformations |
| Session | Manages communication sessions |
| Transport | Provides end-to-end transport services |
| Network | Logical addressing and routing |
| Data Link | Frame-level delivery over a link |
| Physical | Transmits raw bits over the physical medium |

These are descriptions of **roles**. A layer's function is not the same as a specific protocol or product that performs it.

### OSI Revision Trick

| Position | Layer |
|---|---|
| Top layer | Application |
| Bottom layer | Physical |
| Layer 4 | Transport |
| Layer 3 | Network |
| Layer 2 | Data Link |
| Layer 1 | Physical |

Optional mnemonic, top to bottom (first letters A-P-S-T-N-D-P):

> **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing

---

## 6. Connection with NetSentinel-XDR

**Project:** NetSentinel-XDR — Network Monitoring & Threat Detection System

> **Source verification status**
> The project files (`scanner.py`, `network_enumeration.py`, `app.py`, `response_manager.py`, `index.html`) were not available when this section was written. No file-level implementation claim is made below. Before publishing, confirm each item in the verification table against the source code.

### Lecture Concept

Lecture 1b teaches that communication between entities is governed by protocols, that standardization provides common rules, and that the OSI Reference Model organizes network functionality into seven layers.

### Project Implementation

Project behavior must be taken from the code. The table lists candidate connections to be confirmed.

| Candidate Connection | Where to Confirm |
|---|---|
| Observation of ARP, TCP, or ICMP packets | `scanner.py`, `network_enumeration.py` |
| TCP SYN-related detection | `scanner.py` |
| Network reconnaissance detection | `scanner.py`, `network_enumeration.py` |
| Alert generation | `app.py` |
| Response workflow | `response_manager.py` |
| Presentation of alerts to the user | `index.html` |

Include a candidate in the final document only if the code supports it, and describe it using the code's actual behavior.

### Conceptual Connection

These points are general and do not depend on specific files.

- Knowing what a protocol's rules look like makes it possible to recognize when observed traffic follows them or departs from them.
- Network monitoring depends on interpreting protocol information. Understanding protocols is therefore useful background for understanding what a monitoring system reports.
- **Protocol observation is not protocol implementation.** A monitoring tool that observes or analyzes traffic does not implement the protocols it observes.

### Restrictions

- NetSentinel-XDR should not be described as implementing the seven-layer OSI model.
- Lecture 1b does not teach NetSentinel-specific detection features.
- The connection is conceptual: protocol knowledge helps in understanding the project.

---

## 7. Important Diagrams

### Standardization, Layering, and OSI

```text
ISO starts standardization (1977)
              |
              v
First standard of layering architecture (1984)
              |
              v
     OSI Reference Model (7 layers)
```

### Protocol vs Implementation

```text
        PROTOCOL (rules)
              |
              |  followed by
              v
   SOFTWARE / HARDWARE (implementation)
```

### OSI Layers — Reading Direction

```text
Top-to-bottom                Bottom-to-top
(7 -> 1)                     (1 -> 7)

 7 Application                7 Application   ^
 6 Presentation               6 Presentation  |
 5 Session                    5 Session       |
 4 Transport                  4 Transport     |
 3 Network                    3 Network       |
 2 Data Link                  2 Data Link     |
 1 Physical    |              1 Physical      |
               v
```

---

## 8. Exam-Oriented Points

| Concept | Important Point |
|---|---|
| Protocol | Set of communication rules |
| Standardization | Common standards for networking |
| ISO | Standardization process started in 1977, according to the lecture |
| Layered architecture | First standard released in 1984, according to the lecture |
| OSI model | Seven layers |
| Top layer | Application |
| Bottom layer | Physical |
| Layer 3 | Network |
| Layer 4 | Transport |

---

## 9. Important Questions

### 9.1 MCQ-Type Questions

**Q1.** A protocol is best described as:

- A. A physical cable used to connect devices
- B. A set of rules governing communication between communicating entities
- C. A program that stores network data
- D. A device that connects two networks

**Q2.** Which statement correctly distinguishes a protocol from its implementation?

- A. A protocol is the software that carries out communication
- B. A protocol is the hardware used in a network
- C. A protocol is the set of rules; software or hardware implements them
- D. A protocol and its implementation are always the same thing

**Q3.** According to the lecture, which body started the standardization process in 1977?

- A. IEEE
- B. IETF
- C. ITU
- D. ISO

**Q4.** According to the lecture, the first standard of layering architecture was released in:

- A. 1984
- B. 1977
- C. 1990
- D. 1969

**Q5.** OSI stands for:

- A. Open Standard Interface
- B. Open Systems Interconnection
- C. Optimized System Integration
- D. Operational Systems Interchange

**Q6.** How many layers does the OSI Reference Model have?

- A. Four
- B. Five
- C. Seven
- D. Nine

**Q7.** Which is the top layer of the OSI model?

- A. Physical
- B. Presentation
- C. Session
- D. Application

**Q8.** Which layer is Layer 3 of the OSI model?

- A. Network
- B. Transport
- C. Data Link
- D. Session

**Q9.** Which layer is Layer 4 of the OSI model?

- A. Network
- B. Session
- C. Transport
- D. Presentation

**Q10.** Which option lists the OSI layers in the correct top-to-bottom order?

- A. Application, Session, Presentation, Transport, Network, Data Link, Physical
- B. Application, Presentation, Session, Transport, Network, Data Link, Physical
- C. Physical, Data Link, Network, Transport, Session, Presentation, Application
- D. Application, Presentation, Transport, Session, Network, Data Link, Physical

### 9.2 Short-Answer Questions

1. Define a protocol.
2. Why is standardization needed in networking?
3. What two historical events does the lecture associate with 1977 and 1984?
4. List the seven OSI layers from top to bottom.
5. What is the difference between a protocol and the software or hardware that implements it?

### 9.3 Conceptual Questions

1. Two networking systems cannot exchange data because each follows its own rules. Which concept addresses this problem, and what is the intended outcome?
2. Two people greet each other and respond in turn. Which networking concept does the lecture compare this to?
3. A student writes that "Physical" is the top OSI layer and "Application" is Layer 1. Identify the error and give the correct positions.
4. A developer says, "Our program is the protocol." Evaluate this statement.
5. A question asks which layer lies directly below Layer 4 and which lies directly above Layer 2. Identify both.

### 9.4 Answer Key

**MCQ answers**

| Q1 | Q2 | Q3 | Q4 | Q5 | Q6 | Q7 | Q8 | Q9 | Q10 |
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| B | C | D | A | B | C | D | A | C | B |

**Short-answer key**

1. A protocol is a set of rules governing communication between communicating entities.
2. Different networking systems need common rules and specifications so that they can communicate and work together (interoperability).
3. 1977: ISO started the standardization process. 1984: the first standard of layering architecture was released.
4. Application, Presentation, Session, Transport, Network, Data Link, Physical.
5. The protocol is the set of rules; the software or hardware implements and follows those rules.

**Conceptual-question key**

1. Standardization; it establishes common rules so that systems can interoperate.
2. A protocol — rules governing communication between entities.
3. Physical is the bottom layer (Layer 1) and Application is the top layer (Layer 7).
4. Incorrect. The protocol is the set of rules; a program is an implementation of them.
5. Directly below Layer 4 (Transport) is Layer 3 (Network). Directly above Layer 2 (Data Link) is Layer 3 (Network).

---

## 10. Quick Revision

### Protocols

- **Definition:** a set of rules governing communication between communicating entities.
- **Why rules are needed:** both entities must interpret the exchange in the same way.
- Rules (protocol) are distinct from software or hardware (implementation).

### Standardization

- Different networking systems need common standards to work together.
- **1977** — ISO started the standardization process.
- **1984** — First standard of layering architecture was released, according to the lecture.

### OSI Model

- **Full form:** Open Systems Interconnection.
- **Seven layers** (top to bottom): Application (7), Presentation (6), Session (5), Transport (4), Network (3), Data Link (2), Physical (1).
- **Top layer:** Application. **Bottom layer:** Physical.

### Timeline

```text
1977 --> ISO started standardization
1984 --> First standard of layering architecture
```

### Compact OSI Diagram

```text
7 Application
6 Presentation
5 Session
4 Transport
3 Network
2 Data Link
1 Physical
```

---

## 11. Knowledge Check

1. State the definition of a protocol.
2. In what year did ISO start the standardization process, according to the lecture?
3. In what year was the first standard of layering architecture released, according to the lecture?
4. What is the full form of OSI?
5. Write the OSI layers in order from Layer 7 to Layer 1.
6. Which layer number is the Transport layer?
7. Which layer number is the Network layer?
8. Two vendors' systems follow different rules and cannot communicate. What is needed?
9. In an exam diagram, the layer at the very bottom is labelled. What is its name and number?
10. A colleague says the protocol is the code running on a device. How would you correct this?

### Answer Key

| Q | Answer |
|:--:|---|
| 1 | A set of rules governing communication between communicating entities |
| 2 | 1977 |
| 3 | 1984 |
| 4 | Open Systems Interconnection |
| 5 | Application, Presentation, Session, Transport, Network, Data Link, Physical |
| 6 | Layer 4 |
| 7 | Layer 3 |
| 8 | Standardization (common rules) for interoperability |
| 9 | Physical, Layer 1 |
| 10 | The protocol is the set of rules; the code is an implementation of those rules |
