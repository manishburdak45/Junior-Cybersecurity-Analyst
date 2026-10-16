# Section F — ARPANET to TCP/IP

Study notes on the historical path from ARPANET and NCP to TCP/IP, NSFNET, and the closure of ARPANET, prepared for exam revision.

**Labelling convention used in this document**

- Content without a label is derived from Lecture 1b.
- Content marked `Additional Understanding` is general technical knowledge that goes beyond the lecture.
- Dates are given exactly as framed in the lecture.

---

## 1. ARPANET and NCP

### Lecture Description

- ARPANET was using **NCP**, which stands for **Network Control Protocol**.
- The lecture describes NCP as relatively **monolithic**.
- NCP handled functions such as **file transfers** and **connection establishment**.
- This approach worked for a relatively **uniform** network structure.
- Increasing **network diversity** became an issue.

```text
ARPANET
   |
   v
NCP
   |
   +---- File transfers
   |
   +---- Connection establishment
   |
   v
Network diversity becomes a challenge
```

### Key Terms

| Term | Simple Meaning |
|---|---|
| Monolithic | Built as a single, combined whole rather than as separate parts |
| Uniform network | A network whose structure is relatively similar throughout |
| Network diversity | Variety among the networks being connected |

### Reading of the Lecture's Point

The lecture's point is that one protocol handling several functions together suited a uniform network, but became a problem as networks became more varied. The lecture does not go into further technical detail about how NCP was structured, so none is added here.

---

## 2. TCP/IP Development

### 2.1 The 1973 Protocol Proposal

- **1973:** Vint Cerf and Bob Kahn proposed a new protocol.
- The paper was titled:

  *A Protocol for Packet Network Intercommunication*

### 2.2 The 1978 TCP/IP Split

- **1978:** The protocol was split into two protocols, **TCP** and **IP**.

**Additional Understanding — Common Roles of TCP and IP**

These roles are not quoted from the lecture.

| Protocol | Common Role |
|---|---|
| TCP | Transport services and reliable, ordered byte-stream delivery |
| IP | Logical addressing and packet forwarding |

### 2.3 The 1983 ARPANET Transition

- **1983:** ARPANET switched to TCP/IP.

### Distinguishing 1978 from 1983

| Year | Event | Meaning |
|---|---|---|
| 1978 | TCP and IP split | The protocol was divided into two protocols |
| 1983 | ARPANET switched to TCP/IP | ARPANET changed over to using TCP/IP |

The split (1978) and the switch (1983) are separate events. A common error is to date ARPANET's adoption of TCP/IP to 1978.

### Timeline

```text
1973
  |
  v
New protocol proposed
  |
  v
1978
  |
  v
TCP and IP split
  |
  v
1983
  |
  v
ARPANET switches to TCP/IP
```

---

## 3. NSFNET Launch in 1986

### Lecture Points

- **1986:** NSFNET was launched.
- It was a **high-speed academic backbone** created by the **National Science Foundation**.
- It began to **supersede** ARPANET as the core research network.

### Key Terms

| Term | Simple Meaning |
|---|---|
| NSFNET | The network launched in 1986 by the National Science Foundation |
| Academic backbone | A main high-capacity network serving academic and research use |
| Supersede | To take the place of something older |

### Reading of the Lecture's Point

NSFNET began to take over from ARPANET as the core research network. The word "began" matters: the lecture frames this as a developing change, which is followed by ARPANET's closure in 1990.

---

## 4. ARPANET Closure in 1990

### Lecture Points

- **February 28, 1990:** ARPANET was officially decommissioned.
- NSFNET, commercial ISPs, and other networks took over.

### Key Term

| Term | Simple Meaning |
|---|---|
| Decommissioned | Officially taken out of service |

### Important Clarification

The decommissioning ended ARPANET. It does **not** mean that the Internet itself was shut down. According to the lecture, other networks took over.

### Timeline

```text
1983 — ARPANET switches to TCP/IP
             |
             v
1986 — NSFNET launched
             |
             v
February 28, 1990
             |
             v
ARPANET officially decommissioned
```

---

## 5. Complete Historical Timeline

| Year | Event |
|---|---|
| 1973 | Vint Cerf and Bob Kahn proposed a new protocol |
| 1978 | TCP and IP split into two protocols |
| 1983 | ARPANET switched to TCP/IP |
| 1986 | NSFNET launched |
| February 28, 1990 | ARPANET officially decommissioned |

---

## 6. Connection with NetSentinel-XDR

**Project:** NetSentinel-XDR — Network Monitoring & Threat Detection System

> **Source verification status**
> The project files (`scanner.py`, `network_enumeration.py`, `app.py`, `response_manager.py`, `index.html`) were not available when this section was written. No file-level implementation claim is made below. Confirm each item in the table against the source code before publishing.

### Lecture Concept

The lecture traces a historical path: ARPANET used NCP, a new protocol was proposed in 1973, it was split into TCP and IP in 1978, ARPANET switched to TCP/IP in 1983, NSFNET was launched in 1986, and ARPANET was decommissioned on February 28, 1990.

### Project Implementation

Project behavior must be taken from the code. The table lists candidate connections to be confirmed.

| Candidate Connection | Where to Confirm |
|---|---|
| TCP traffic observation | `scanner.py` |
| TCP SYN-related activity detection | `scanner.py` |
| ARP packet observation | `network_enumeration.py` |
| ICMP packet observation | `scanner.py`, `network_enumeration.py` |
| Network reconnaissance detection | `scanner.py`, `network_enumeration.py` |
| Alert generation | `app.py` |
| Response workflow | `response_manager.py` |
| Display of results | `index.html` |

Include an item only if the code supports it, and describe it using the code's actual behavior.

### Conceptual Connection

These points are general and do not depend on specific files.

- Modern networks widely use TCP/IP, which is the outcome of the history in this section. Understanding that background helps a student recognize the protocols a network monitoring tool reports on.
- A monitoring system that observes TCP, ARP, or ICMP traffic is reading protocol information. Observing a protocol is not the same as implementing it.
- Observing TCP SYN-related activity indicates connection-related activity. A packet observation does **not** prove successful application-level delivery.

### Restrictions

- NetSentinel-XDR should not be described as implementing the complete TCP/IP stack.
- The project does not reproduce ARPANET or NSFNET.
- The historical events in the lecture do not correspond to specific project features. The connection is conceptual, and historical content is kept separate from modern project functionality.

---

## 7. Important Diagrams

### ARPANET with NCP

```text
ARPANET
   |
   v
NCP
   |
   +---- File transfers
   |
   +---- Connection establishment
   |
   v
Network diversity becomes a challenge
```

### Protocol Development, 1973 to 1983

```text
1973  New protocol proposed (Vint Cerf and Bob Kahn)
  |
  v
1978  TCP and IP split
  |
  v
1983  ARPANET switches to TCP/IP
```

### Full Chronology

```text
1973 ---- 1978 ---- 1983 ---- 1986 ---- Feb 28, 1990
 |         |         |         |            |
 |         |         |         |            +-- ARPANET officially
 |         |         |         |                decommissioned
 |         |         |         +-- NSFNET launched
 |         |         +-- ARPANET switches to TCP/IP
 |         +-- TCP and IP split
 +-- New protocol proposed
```

---

## 8. Exam-Oriented Points

| Concept | Key Point |
|---|---|
| NCP | Earlier ARPANET protocol |
| NCP limitation | Monolithic approach; network diversity became an issue |
| 1973 | New protocol proposed by Vint Cerf and Bob Kahn |
| 1978 | TCP and IP split |
| 1983 | ARPANET switches to TCP/IP |
| 1986 | NSFNET launched |
| February 28, 1990 | ARPANET officially decommissioned |

---

## 9. Important Questions

### 9.1 MCQ-Type Questions

**Q1.** NCP stands for:

- A. Node Connection Protocol
- B. Network Communication Procedure
- C. Network Control Protocol
- D. Networked Control Process

**Q2.** According to the lecture, who proposed a new protocol in 1973?

- A. Vint Cerf and Bob Kahn
- B. Tim Berners-Lee and Vint Cerf
- C. Bob Kahn and Leonard Kleinrock
- D. The National Science Foundation

**Q3.** The 1973 paper was titled:

- A. Packet Switching in Wide Area Networks
- B. A Protocol for Network Control
- C. Network Intercommunication Using Packets
- D. A Protocol for Packet Network Intercommunication

**Q4.** According to the lecture, the protocol was split into TCP and IP in:

- A. 1973
- B. 1978
- C. 1983
- D. 1986

**Q5.** According to the lecture, ARPANET switched to TCP/IP in:

- A. 1978
- B. 1973
- C. 1983
- D. 1990

**Q6.** NSFNET was launched in:

- A. 1986
- B. 1983
- C. 1990
- D. 1978

**Q7.** Which description of NSFNET matches the lecture?

- A. The earlier ARPANET protocol
- B. A commercial ISP
- C. The protocol that was split into TCP and IP
- D. A high-speed academic backbone created by the National Science Foundation

**Q8.** Which statement matches the lecture's description of the NCP approach?

- A. It was divided into too many separate protocols
- B. It was a monolithic approach, and increasing network diversity became an issue
- C. It could not handle file transfers
- D. It was launched in 1986 as an academic backbone

**Q9.** ARPANET was officially decommissioned on:

- A. January 1, 1983
- B. December 1986
- C. February 28, 1990
- D. 1978

**Q10.** Which sequence of events is in the correct chronological order?

- A. Protocol proposed, TCP and IP split, ARPANET switches to TCP/IP, NSFNET launched, ARPANET decommissioned
- B. TCP and IP split, protocol proposed, NSFNET launched, ARPANET switches to TCP/IP, ARPANET decommissioned
- C. ARPANET switches to TCP/IP, TCP and IP split, protocol proposed, NSFNET launched, ARPANET decommissioned
- D. Protocol proposed, ARPANET switches to TCP/IP, TCP and IP split, ARPANET decommissioned, NSFNET launched

### 9.2 Short-Answer Questions

1. What does NCP stand for, and what functions did it handle according to the lecture?
2. What limitation of the NCP approach does the lecture highlight?
3. What is the difference between the 1978 event and the 1983 event?
4. What was NSFNET, and what did it begin to do in relation to ARPANET?
5. What happened on February 28, 1990, and which networks took over?

### 9.3 Scenario-Based Questions

1. A protocol design handles file transfers and connection establishment together. It works well for a relatively uniform network but struggles as networks become more varied. Which protocol and which characteristic does this describe?
2. In 1973, two researchers publish a paper proposing a new protocol for connecting packet networks. Which event is this, and who were the researchers?
3. A student writes that ARPANET adopted TCP/IP in 1978. Is this correct?
4. A high-speed academic backbone created by the National Science Foundation begins to take over from ARPANET as the core research network. Which network is this, and in which year was it launched?
5. A student claims that the Internet was shut down on February 28, 1990, because ARPANET was decommissioned. Evaluate this claim.

### 9.4 Answer Key

**MCQ answers**

| Q1 | Q2 | Q3 | Q4 | Q5 | Q6 | Q7 | Q8 | Q9 | Q10 |
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| C | A | D | B | C | A | D | B | C | A |

**Short-answer key**

1. Network Control Protocol. It handled functions such as file transfers and connection establishment.
2. It was a relatively monolithic approach. It worked for a relatively uniform network structure, but increasing network diversity became an issue.
3. 1978: the protocol was split into two protocols, TCP and IP. 1983: ARPANET switched to TCP/IP.
4. NSFNET was a high-speed academic backbone created by the National Science Foundation, launched in 1986. It began to supersede ARPANET as the core research network.
5. ARPANET was officially decommissioned. NSFNET, commercial ISPs, and other networks took over.

**Scenario key**

1. NCP; its monolithic approach.
2. The 1973 protocol proposal by Vint Cerf and Bob Kahn.
3. No. 1978 is when TCP and IP were split. ARPANET switched to TCP/IP in 1983.
4. NSFNET; 1986.
5. Incorrect. ARPANET was decommissioned, not the Internet. According to the lecture, NSFNET, commercial ISPs, and other networks took over.

---

## 10. Quick Revision

### NCP and Its Limitations

- NCP — Network Control Protocol, used by ARPANET.
- Described as relatively monolithic.
- Handled file transfers and connection establishment.
- Worked for a relatively uniform network structure.
- Increasing network diversity became an issue.

### Key Events

| Year | Event |
|---|---|
| 1973 | Vint Cerf and Bob Kahn proposed a new protocol, in the paper *A Protocol for Packet Network Intercommunication* |
| 1978 | TCP and IP split into two protocols |
| 1983 | ARPANET switched to TCP/IP |
| 1986 | NSFNET launched as a high-speed academic backbone by the National Science Foundation |
| February 28, 1990 | ARPANET officially decommissioned; NSFNET, commercial ISPs, and other networks took over |

### Chronological Timeline

```text
1973 --> New protocol proposed
1978 --> TCP and IP split
1983 --> ARPANET switches to TCP/IP
1986 --> NSFNET launched
1990 --> ARPANET officially decommissioned (February 28)
```

---

## 11. Knowledge Check

1. What does NCP stand for?
2. Name the two researchers who proposed a new protocol in 1973.
3. In what year was the protocol split into TCP and IP?
4. In what year did ARPANET switch to TCP/IP?
5. Which organization created NSFNET, and in what year was it launched?
6. What does it mean for a network to be officially decommissioned?
7. Put these in chronological order: NSFNET launched, TCP and IP split, ARPANET decommissioned, protocol proposed.
8. Why did increasing network diversity matter for the NCP approach, according to the lecture?
9. What is a commonly stated role of TCP? (Additional Understanding)
10. What is a commonly stated role of IP? (Additional Understanding)

### Answer Key

| Q | Answer |
|:--:|---|
| 1 | Network Control Protocol |
| 2 | Vint Cerf and Bob Kahn |
| 3 | 1978 |
| 4 | 1983 |
| 5 | National Science Foundation; 1986 |
| 6 | It is officially taken out of service |
| 7 | Protocol proposed, TCP and IP split, NSFNET launched, ARPANET decommissioned |
| 8 | NCP was relatively monolithic and suited a relatively uniform network; increasing diversity became an issue |
| 9 | Transport services and reliable, ordered byte-stream delivery |
| 10 | Logical addressing and packet forwarding |
