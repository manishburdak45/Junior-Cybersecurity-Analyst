# Section E — Network Layering and Interconnectivity

Study notes on network layering, data units, error detection, TCP acknowledgements, and interconnectivity issues, prepared for exam revision.

**Labelling convention used in this document**

- Content under a heading without a label is derived from Lecture 1b: the OSI seven-layer diagram and the interconnectivity questions.
- Content marked `Additional Understanding` is general networking knowledge that goes beyond the lecture.
- Encapsulation, data units, checksums, and TCP acknowledgements are treated as additional knowledge throughout.

---

## 1. Network Layering

### Lecture-Supported

The lecture presents the **OSI seven-layer diagram** as a layered view of network functionality (see Section 2).

### Additional Understanding

**What network layering means.** Network layering organizes communication functionality into a stack of layers. Each layer has a defined role, and layers are ordered from the one closest to the application to the one closest to the physical medium.

**Why functionality can be organized into layers.** Communication involves many separate concerns, such as producing data, moving it between networks, and sending signals over a medium. Grouping related concerns into layers lets each concern be described and studied separately.

**How the OSI model provides a layered view.** The OSI model names seven layers and numbers them 7 to 1. This gives a common way to refer to where a function belongs.

**How layering helps reasoning.** When a problem occurs, a student can ask which layer's responsibility it falls under. This narrows the question from "why did communication fail?" to a more specific one.

### Sender, Network, and Receiver

```text
+----------+          +-----------+          +------------+
|  Sender  | -------> |  Network  | -------> |  Receiver  |
| (layers) |          |           |          |  (layers)  |
+----------+          +-----------+          +------------+
```

Both the sender and the receiver process data through a stack of layers.

---

## 2. OSI Layering Review

### Seven Layers

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

### Ordering

- **Top to bottom (7 to 1):** Application, Presentation, Session, Transport, Network, Data Link, Physical.
- **Bottom to top (1 to 7):** Physical, Data Link, Network, Transport, Session, Presentation, Application.
- **Topmost layer:** Layer 7 — Application.
- **Bottommost layer:** Layer 1 — Physical.

The lecture slide lists the layers. It does not give a detailed explanation of each layer's function, so none is given here.

---

## 3. Encapsulation and Decapsulation

This section is **Additional Understanding**. It is not explained in the lecture.

### Encapsulation

Encapsulation happens on the **sender**. As data moves down through the layers of the sender's networking stack, each layer adds its own protocol information, commonly in the form of a **header**. The original data carried by a layer is its **payload**.

```text
Application Data
      |
      v
Transport Information Added
      |
      v
Network Information Added
      |
      v
Frame Created
      |
      v
Bits Transmitted
```

### Decapsulation

Decapsulation happens on the **receiver**. As incoming data moves up through the layers, each layer processes the protocol information intended for it and passes the payload upward.

```text
Bits Received
      |
      v
Frame Processed
      |
      v
Network Information Processed
      |
      v
Transport Information Processed
      |
      v
Application Data Delivered
```

### Comparison

| Aspect | Encapsulation | Decapsulation |
|---|---|---|
| Performed by | Sender | Receiver |
| Direction in stack | Downward | Upward |
| Protocol information | Added | Processed |

### Note

These are **simplified diagrams**. Actual processing depends on the protocol stack in use.

---

## 4. Data Units

This section is **Additional Understanding**. It is not presented as an explicit statement of the lecture.

| Data Unit | Associated Layer | Basic Explanation |
|---|---|---|
| Bits | Physical | Raw bits transmitted over a medium |
| Frame | Data Link | Data unit handled at the link layer |
| Packet | Network | Data unit associated with the network layer |
| Segment | Transport | Common term for a TCP transport data unit |

### Receiver-Side Sequence

```text
Bits --> Frame --> Packet --> Segment --> Application Data
```

### Terminology Note

- `Segment` is commonly used for TCP.
- UDP commonly uses the term `datagram`.
- Terms are used with some variation in practice, so the associations above are the common ones.

---

## 5. Transmission Error Detection and Checksums

This section is **Additional Understanding**. It is not explained in the lecture.

### Why Data May Be Corrupted

Data crosses a physical medium and passes through network equipment. Along the way, interference or faults can change some of the transmitted bits, so the data received may differ from the data sent.

### What a Checksum Is

A checksum is a small value calculated from the data. It is sent along with the data so that the receiver can check the data it receives.

### How It Works

1. The sender calculates a checksum from the data.
2. The sender transmits the data together with the checksum.
3. The receiver recalculates or verifies the checksum using the data it received.
4. The receiver compares the result with the expected check.

```text
Sender
  |
  v
Data + Checksum
  |
  v
Transmission
  |
  v
Receiver
  |
  v
Recalculate / Verify
  |
  +---- Expected check passes
  |
  +---- Mismatch indicates a possible error
```

### What a Mismatch May Indicate

A mismatch indicates that the data, or the checksum itself, **may** have been changed during transmission. It signals a possible error. It does not identify which bits changed.

### Limitation

A checksum does **not guarantee detection of every possible error**. Some changes can alter the data in a way that still produces a matching check. A passing check therefore shows that no error was detected, not that none occurred.

---

## 6. TCP Acknowledgement

This section is **Additional Understanding**. It is not explained in the lecture.

### Why Acknowledgements Are Used

A sender cannot see whether data arrived. An acknowledgement (ACK) is a message from the receiver that tells the sender that specified TCP data has been received.

```text
Sender                         Receiver
  |                                |
  |---------- TCP Data ----------->|
  |                                |
  |<----------- TCP ACK ------------|
```

### What Receipt of an ACK Indicates

When the sender receives an ACK for specified TCP data, it indicates that the receiving side's TCP acknowledged **receipt** of that data.

### What an ACK Does Not Prove

| Statement | Why It Is Not Guaranteed |
|---|---|
| The receiving application has processed the message | The ACK concerns receipt of TCP data. It says nothing about whether the application has used it. |
| The physical cable is perfect | An ACK shows that exchange worked for that data. It does not show the condition of the medium. |
| The destination host will remain online | An ACK reflects what happened up to that moment. The host can go offline afterwards. |

### Exam Caution

Conceptual MCQs often offer a conclusion that is **stronger** than what an ACK supports. The safe interpretation is limited to acknowledged receipt of specified TCP data.

---

## 7. Interconnectivity Issues

### Lecture Questions

The lecture presents an interconnectivity diagram involving hosts including **H1**, **H3**, and **H5**, and raises these questions:

1. How does H3 understand that H5 is still connected to the network and that H1 can reach H5?
2. How does H3 understand that the message was indeed delivered?
3. Did the message reach as H3 sent it?

> The exact layout of the lecture's diagram is not reproduced here. The illustration below is a simplified aid and is not the lecture's diagram.

```text
  H3 -----+
          |
       Network ------ H5
          |
  H1 -----+
```

### Significance of the Questions

The three questions concern different things. Each one can be answered "yes" or "no" independently of the others.

| Lecture Question | Concern |
|---|---|
| Is H5 still connected, and can H1 reach H5? | Reachability |
| Was the message indeed delivered? | Delivery confirmation |
| Did the message reach as H3 sent it? | Data integrity |

### 7.1 Reachability

**The problem.** Before or while communicating, a sender may need to know whether a destination is still connected and whether it can be reached.

**Additional Understanding**

- Routing and the current state of the network affect whether a destination can be reached.
- Diagnostic tools such as **ping** can help check reachability.
- Ping failure does **not always prove** that a host is disconnected. The host may be reachable while not responding to the probe.

### 7.2 Delivery Confirmation

**The problem.** Sending data is different from knowing it was received. The sender needs some way to learn that the message arrived.

**Additional Understanding**

- Acknowledgements provide this feedback (see Section 6).
- An acknowledgement confirms receipt of specified data, within the limits described earlier. It does not guarantee processing by the application.

### 7.3 Data Integrity

**The problem.** Even when a message arrives, it may differ from what was sent.

**Additional Understanding**

- Checksums and other error-detection techniques help identify possible corruption (see Section 5).
- Detection is not perfect: a checksum cannot guarantee that every error will be found.

### Scope Note

The lecture raises the questions above. The explanations of ping, acknowledgements, and checksums are additional knowledge. They should not be read as the lecture's own solutions.

---

## 8. Connection with NetSentinel-XDR

**Project:** NetSentinel-XDR — Network Monitoring & Threat Detection System

> **Source verification status**
> The project files (`scanner.py`, `network_enumeration.py`, `app.py`, `response_manager.py`, `index.html`) were not available when this section was written. No file-level implementation claim is made below. Confirm each item in the table against the source code before publishing.

### Lecture Concept

The lecture presents the OSI layers and raises questions about reachability, delivery, and whether a message arrives as sent. These concepts describe how communication is organized and what a sender can and cannot know about it.

### Project Implementation

Project behavior must be taken from the code. The table lists candidate connections to be confirmed.

| Candidate Connection | Where to Confirm |
|---|---|
| Network traffic observation | `scanner.py` |
| ARP-based device discovery | `network_enumeration.py` |
| TCP SYN-related activity detection | `scanner.py` |
| ICMP observations and host discovery | `scanner.py`, `network_enumeration.py` |
| Network reconnaissance detection | `scanner.py`, `network_enumeration.py` |
| Alert generation | `app.py` |
| Response workflow | `response_manager.py` |
| Display of results | `index.html` |

Include an item only if the code supports it, and describe it using the code's actual behavior.

### Conceptual Connection

These points are general and do not depend on specific files.

- Knowing that protocols such as ARP, ICMP, and TCP operate at different layers helps a student interpret what a monitoring tool reports. This is a conceptual reading aid, not a statement that the project is organized by layer.
- Reachability and host discovery relate to the lecture's first question: how a system learns that another host is present and can be reached.
- Observing a TCP SYN indicates connection-related activity. It does not show what happened afterwards.
- Observing a packet is different from confirming delivery. Packet observation does **not** prove successful application-level delivery.

### Restrictions

- NetSentinel-XDR should not be described as implementing all seven OSI layers as separate modules.
- The project should not be described as verifying the integrity of every transmitted message unless the source code explicitly does so.
- The lecture does not teach NetSentinel-specific features. The connection is conceptual.

---

## 9. Important Diagrams

### Sender, Network, and Receiver

```text
+----------+          +-----------+          +------------+
|  Sender  | -------> |  Network  | -------> |  Receiver  |
+----------+          +-----------+          +------------+
```

### Sender Side (Encapsulation) and Receiver Side (Decapsulation)

```text
Sender (down the stack)         Receiver (up the stack)

Application Data                Application Data Delivered
      |                                ^
Transport Info Added            Transport Info Processed
      |                                ^
Network Info Added              Network Info Processed
      |                                ^
Frame Created                   Frame Processed
      |                                ^
Bits Transmitted  ------------> Bits Received
```

Simplified diagram. Actual processing depends on the protocol stack.

### Data Units by Layer

```text
Transport   -->  Segment
Network     -->  Packet
Data Link   -->  Frame
Physical    -->  Bits
```

### Checksum Verification

```text
Data + Checksum --> Transmission --> Receiver verifies
                                         |
                       +-----------------+-----------------+
                       |                                   |
              Expected check passes         Mismatch: possible error
```

### TCP Acknowledgement

```text
Sender                         Receiver
  |---------- TCP Data ----------->|
  |<----------- TCP ACK ------------|
```

### Lecture's Three Interconnectivity Concerns

```text
Reachability  -->  Is H5 connected, and can H1 reach H5?
Delivery      -->  Was the message indeed delivered?
Integrity     -->  Did the message reach as H3 sent it?
```

---

## 10. Exam-Oriented Points

| Concept | Key Point |
|---|---|
| Encapsulation | Sender-side protocol information is added as data moves down the stack |
| Decapsulation | Receiver processes protocol information as data moves up the stack |
| Bits | Physical layer |
| Frame | Data Link layer |
| Packet | Network layer |
| TCP Segment | Transport layer |
| Checksum | Helps detect possible errors |
| TCP ACK | Acknowledges receipt of specified TCP data |
| Reachability | Whether a destination can be reached |
| Delivery confirmation | Whether data has been received |
| Data integrity | Whether data has remained correct |

---

## 11. Important Questions

### 11.1 MCQ-Type Questions

**Q1.** Which sequence correctly shows the receiver-side data units?

- A. Segment, Packet, Frame, Bits, Application Data
- B. Bits, Frame, Packet, Segment, Application Data
- C. Frame, Bits, Segment, Packet, Application Data
- D. Packet, Segment, Frame, Bits, Application Data

**Q2.** The process in which a sender's stack adds protocol information, such as headers, as data moves down the layers is called:

- A. Decapsulation
- B. Checksumming
- C. Encapsulation
- D. Acknowledgement

**Q3.** Which data unit is handled at the Data Link layer?

- A. Bits
- B. Packet
- C. Segment
- D. Frame

**Q4.** Which layer and data-unit pairing is correct?

- A. Transport — Frame
- B. Network — Segment
- C. Physical — Bits
- D. Data Link — Packet

**Q5.** A receiver finds that a checksum does not match. This most appropriately indicates that:

- A. The destination host is offline
- B. The data may have been changed during transmission
- C. The physical cable is working perfectly
- D. The receiving application has crashed

**Q6.** Which statement about checksums is correct?

- A. A matching checksum guarantees the data is error-free
- B. A checksum prevents corruption from occurring
- C. A checksum helps detect possible errors but cannot guarantee detection of every error
- D. A checksum confirms the application processed the data

**Q7.** A sender receives a TCP ACK. Which conclusion is justified?

- A. The receiving application has processed the message
- B. The physical cable is perfect
- C. The destination host will remain online
- D. The receiving side acknowledged receipt of the specified TCP data

**Q8.** Which of the lecture's questions concerns data integrity?

- A. Did the message reach as H3 sent it?
- B. How does H3 understand that H5 is still connected to the network?
- C. How does H3 understand that the message was indeed delivered?
- D. Which layer is topmost in the OSI model?

**Q9.** In the OSI model as shown in the lecture, which layer is Layer 4?

- A. Session
- B. Transport
- C. Network
- D. Presentation

**Q10.** A ping to a host receives no reply. Which statement is most appropriate?

- A. The host may be unreachable, but ping failure does not always prove it is disconnected
- B. The host is definitely disconnected from the network
- C. The physical cable is definitely cut
- D. The message was delivered but corrupted

### 11.2 Short-Answer Questions

1. What is encapsulation?
2. Name the data unit associated with each of these layers: Physical, Data Link, Network, Transport.
3. What is a checksum, and what does a mismatch indicate?
4. What does a TCP ACK indicate?
5. State the three questions raised by the lecture's interconnectivity diagram.

### 11.3 Scenario-Based Questions

1. H3 wants to know whether H5 is still connected and whether H1 can reach H5. Which concept does this describe?
2. A sender wants to learn whether its data has been received by the destination. Which concept does this describe, and which TCP feature is relevant?
3. A receiver recalculates a check value and finds it differs from the expected one. Which concept is involved, and what does the result suggest?
4. A sender receives a TCP ACK, but the user at the destination reports that the message never appeared on screen. Which limitation of ACKs does this illustrate?
5. A receiver takes raw bits from the medium, forms a link-level data unit, and then passes the contents upward. Which process is this, and which data unit comes immediately after bits?

### 11.4 Answer Key

**MCQ answers**

| Q1 | Q2 | Q3 | Q4 | Q5 | Q6 | Q7 | Q8 | Q9 | Q10 |
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| B | C | D | C | B | C | D | A | B | A |

**Short-answer key**

1. Encapsulation is the sender-side process of adding protocol information, such as headers, as data moves down the layers of the stack.
2. Physical: bits. Data Link: frame. Network: packet. Transport: segment (commonly for TCP).
3. A checksum is a small value calculated from data and sent with it so the receiver can check the data. A mismatch indicates a possible error.
4. It indicates that the receiving side acknowledged receipt of the specified TCP data.
5. (a) How does H3 understand that H5 is still connected to the network and that H1 can reach H5? (b) How does H3 understand that the message was indeed delivered? (c) Did the message reach as H3 sent it?

**Scenario key**

1. Reachability.
2. Delivery confirmation; TCP acknowledgement.
3. Data integrity and error detection; a possible error in the received data.
4. An ACK does not prove that the receiving application has processed the message.
5. Decapsulation; the frame.

---

## 12. Quick Revision

### OSI Layer Order

Top to bottom: Application (7), Presentation (6), Session (5), Transport (4), Network (3), Data Link (2), Physical (1).

### Encapsulation vs Decapsulation

| | Encapsulation | Decapsulation |
|---|---|---|
| Side | Sender | Receiver |
| Direction | Down the stack | Up the stack |
| Protocol information | Added | Processed |

### Data-Unit Sequence (Receiver Side)

Bits, Frame, Packet, Segment, Application Data.

### Checksum

- Purpose: help detect possible errors.
- A mismatch indicates a possible error.
- It cannot guarantee detection of every error.

### TCP ACK

- Meaning: acknowledges receipt of specified TCP data.
- Does not prove application processing, a perfect cable, or that the host stays online.

### Reachability, Delivery Confirmation, Data Integrity

| Concept | Question |
|---|---|
| Reachability | Can the destination be reached? |
| Delivery confirmation | Was the data received? |
| Data integrity | Is the received data the same as what was sent? |

### Receiver-Side Data Flow

```text
Bits --> Frame --> Packet --> Segment --> Application Data
```

---

## 13. Knowledge Check

1. List the OSI layers from Layer 7 to Layer 1.
2. Which OSI layer is the bottommost, and what is its number?
3. Which process adds protocol information as data moves down the sender's stack?
4. Which data unit is associated with the Network layer?
5. Which data unit is commonly used for a TCP transport unit, and what term does UDP commonly use?
6. What does a checksum mismatch suggest?
7. Does a matching checksum guarantee that no error occurred?
8. A sender receives a TCP ACK. Can it conclude that the receiving application has processed the message?
9. H3 asks, "Did the message reach as H3 sent it?" Which concept does this question concern?
10. H3 sent a message and wants to know whether it was indeed delivered. Which concept is this, and which TCP feature relates to it?

### Answer Key

| Q | Answer |
|:--:|---|
| 1 | Application, Presentation, Session, Transport, Network, Data Link, Physical |
| 2 | Physical, Layer 1 |
| 3 | Encapsulation |
| 4 | Packet |
| 5 | Segment; datagram |
| 6 | A possible error in the received data |
| 7 | No; a checksum cannot guarantee detection of every error |
| 8 | No; an ACK concerns receipt of TCP data, not application processing |
| 9 | Data integrity |
| 10 | Delivery confirmation; TCP acknowledgement |
