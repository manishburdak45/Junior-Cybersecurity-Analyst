# Section G & H — Interconnectivity Issues and Request for Comments (RFC)

Study notes on interconnectivity issues and Request for Comments (RFC) documents, prepared for exam revision.

**Labelling convention used in this document**

- Content without a label is derived from Lecture 1b.
- Content marked `Additional Understanding` is general technical knowledge that goes beyond the lecture.
- Where a detail could not be verified from the available material, this is stated explicitly.

---

## Part I — Section G: Interconnectivity Issues

### 1. Lecture-Based Introduction

The lecture presents an interconnectivity diagram with hosts **H1, H2, H3, H4, and H5**, and raises three questions:

1. How does H3 understand that H5 is still connected to the network and that H1 can reach H5?
2. How does H3 understand that the message was indeed delivered?
3. Did the message reach as H3 sent it?

These questions introduce important issues involved in network communication. The lecture poses them as questions. It does not provide their solutions in the material used for these notes.

> The exact links and topology of the lecture's diagram are not reproduced here. The diagram below is a conceptual illustration using the same host labels and is not the lecture's diagram.

```text
  H1      H2      H3
   \       |       /
    \      |      /
     +-- Network --+
    /              \
   /                \
  H4                H5
```

| Lecture Question | Issue Raised |
|---|---|
| Is H5 still connected, and can H1 reach H5? | Reachability |
| Was the message indeed delivered? | Delivery confirmation |
| Did the message reach as H3 sent it? | Data integrity |

---

### 2. Reachability

**Question:** Can the destination be reached through the network?

**Meaning.** Reachability is whether a destination can be reached from a source across the network.

**Attempting communication vs knowing reachability.** Sending data toward a destination is an attempt. It is different from knowing that the destination is connected and that a path to it exists.

**Why destination connectivity matters.** A sender needs to know whether the destination is still connected before it can rely on communicating with it. This is the concern of the lecture's first question.

```text
Source ----------- Network ----------> Destination
  H3                                       H5
        Can the destination be reached?
```

**Additional Understanding**

- **Routing and network paths.** Whether a destination can be reached depends on there being a usable path through the network. Routing is involved in determining paths.
- **Diagnostic tools.** A tool such as **ping** can be used to check whether a destination responds.
- **Failed ping.** A failed ping does **not always prove** that a host is disconnected. The host may be reachable but not responding to the probe, or the probe may not have completed for another reason.

---

### 3. Delivery Confirmation

**Question:** How can the sender determine whether a message was delivered?

**Sending vs receiving confirmation.** Sending data does not tell the sender that the data arrived. Knowing that data was received requires some form of confirmation coming back to the sender.

**General concept.** Delivery confirmation is the sender learning that its data was received.

```text
Sender                         Receiver
  |                                |
  |---------- Message ------------>|
  |                                |
  |<-------- Confirmation ---------|
```

**Additional Understanding**

- **Acknowledgements.** An acknowledgement is a message from the receiver indicating that data was received.
- **TCP acknowledgement (ACK).** A TCP ACK acknowledges specified TCP data at the receiving TCP endpoint. When the sender receives it, that indicates the data was acknowledged as received.
- **Limit of an ACK.** A TCP ACK does not necessarily prove that the receiving **application** has processed the message.

| Level | What Is Established |
|---|---|
| TCP level | Specified TCP data was acknowledged by the receiving TCP endpoint |
| Application level | Not established by the ACK alone |

---

### 4. Data Integrity

**Question:** Did the message reach the destination in the same form as it was sent?

**Meaning.** Data integrity is whether the received data remains correct, that is, the same as the data that was sent.

**Possibility of corruption.** Data may be altered during transmission, so what arrives can differ from what was sent.

**Delivery vs correctness.** A message can be delivered and still be incorrect. Receipt of data and correctness of the received data are separate matters. This is the concern of the lecture's third question.

```text
Original Data
     |
     v
Transmission
     |
     v
Received Data
     |
     v
Integrity / Error Check
```

**Additional Understanding**

- **Error detection.** Error-detection techniques help the receiver identify that received data may have been corrupted.
- **Checksums.** A checksum is a small value calculated from data and sent with it. The receiver recalculates or verifies it and compares. A mismatch indicates a possible error.
- **Limitation.** A checksum does **not guarantee detection of every possible error**. A passing check means no error was detected, not that none occurred.

---

### 5. Comparison Table

| Concept | Main Question | Related Mechanism |
|---|---|---|
| Reachability | Can the destination be reached? | Routing, connectivity checks |
| Delivery confirmation | Was the data received? | Acknowledgements |
| Data integrity | Did the data remain correct? | Checksums and error detection |

The **Main Question** column corresponds to the lecture's questions. The **Related Mechanism** column is **Additional Understanding**: the lecture poses the questions and the mechanisms are additional technical knowledge.

---

## Part II — Section H: Request for Comments (RFC)

### 6. What Is an RFC?

**RFC — Request for Comments**

**Additional Understanding**

RFC documents are used to record Internet-related protocols, technical specifications, ideas, and information. Not every RFC is an Internet Standard. RFCs differ in kind, as the four types in Section 8 show.

---

### 7. History of RFCs

According to Lecture 1b:

| Item | Detail |
|---|---|
| First RFC title | "Host Software" |
| Published by | Steve Crocker |

**Additional Understanding — Historical Importance**

The first RFC marks the starting point of the RFC series. RFCs have since served as a record of technical ideas and specifications concerning Internet protocols. The lecture slide gives the title and author; it is not relied on here for further historical detail.

---

### 8. Types of RFCs

The lecture lists four types:

1. Standards
2. Experimental
3. Informational
4. Historical

**Additional Understanding — Basic Meanings**

The lecture slide lists the categories but does not provide detailed definitions. The meanings below are additional.

| RFC Type | Basic Meaning |
|---|---|
| Standards | Documents related to Internet standards and specifications |
| Experimental | Documents related to experimental work |
| Informational | Documents providing technical information |
| Historical | Documents preserving historical technical information |

The four types do not all have the same status or purpose.

---

### 9. RFC Count

The lecture contains a slide titled **RFC Count**.

**Verification status.** The content of that slide was not available when these notes were prepared. The precise value, range, or graph message therefore **cannot be verified from the available material**, and no count is stated here.

To complete this section, read the slide and record only what is clearly visible: an exact value, a range, or the key message of any graph.

**Additional Understanding**

RFC numbers and the overall count increase as new documents are published.

---

## 10. Connection with NetSentinel-XDR

**Project:** NetSentinel-XDR — Network Monitoring & Threat Detection System

> **Source verification status**
> The project files (`scanner.py`, `network_enumeration.py`, `app.py`, `response_manager.py`, `index.html`) were not available when this section was written. No file-level implementation claim is made below. Confirm each item in the table against the source code before publishing.

### Lecture Concept

Section G raises the issues of reachability, delivery confirmation, and data integrity. Section H presents RFCs, with the first titled "Host Software" and four types: Standards, Experimental, Informational, and Historical.

### Project Implementation

Project behavior must be taken from the code. The table lists candidate connections to be confirmed.

| Candidate Connection | Where to Confirm |
|---|---|
| Device discovery and reachability | `network_enumeration.py` |
| ARP-based host discovery | `network_enumeration.py` |
| ICMP observations | `scanner.py`, `network_enumeration.py` |
| TCP SYN-related activity detection | `scanner.py` |
| Network reconnaissance alerts | `scanner.py`, `app.py` |
| Detection and response workflow | `app.py`, `response_manager.py` |
| Display of results | `index.html` |

Include an item only if the code supports it, and describe it using the code's actual behavior.

### Conceptual Connection

These points are general and do not depend on specific files.

- **Reachability.** Discovering which hosts are present on a network relates to the question of whether a destination can be reached. A discovery result describes what was observed at that time.
- **Delivery.** Observing a packet does **not** prove successful application-level delivery.
- **Integrity.** The notes make no claim that NetSentinel verifies the integrity of every message.
- **RFCs (Additional Understanding).** RFCs are a reference for understanding how protocols are specified and expected to behave. This is a general point about documentation. The project does not publish or implement RFCs.

### Restrictions

- No meaningful implementation connection is claimed for the RFC history itself (the first RFC or the RFC types).
- Lecture facts, source-code facts, and additional interpretation are kept separate. Source-code facts are pending verification.

---

## 11. Exam-Oriented Points

### Section G

| Concept | Key Point |
|---|---|
| Reachability | Whether a destination can be reached |
| Delivery confirmation | Whether data was received |
| Data integrity | Whether received data remains correct |
| TCP ACK | Acknowledges specified TCP data at the receiving TCP endpoint (Additional Understanding) |
| Checksum | Helps detect possible data corruption (Additional Understanding) |

### Section H

| Concept | Key Point |
|---|---|
| RFC | Request for Comments |
| First RFC | Host Software |
| Author of first RFC | Steve Crocker |
| Types of RFCs | Standards, Experimental, Informational, Historical |
| RFC Count | The lecture has an RFC Count slide; its value is not verified in these notes |

---

## 12. Important Questions

### 12.1 MCQ-Type Questions

**Q1.** Which of the lecture's questions concerns reachability?

- A. Did the message reach as H3 sent it?
- B. How does H3 understand that H5 is still connected to the network and that H1 can reach H5?
- C. How does H3 understand that the message was indeed delivered?
- D. Which protocol was used first?

**Q2.** A sender wants to know whether its data was received. Which concept does this describe?

- A. Reachability
- B. Data integrity
- C. Delivery confirmation
- D. Error correction

**Q3.** A sender receives a TCP ACK for specified data. Which conclusion is justified?

- A. The receiving application has processed the message
- B. The physical cable is perfect
- C. The destination will always remain connected
- D. The specified TCP data was acknowledged at the receiving TCP endpoint

**Q4.** A message is delivered, but its contents differ from what was sent. Which concept is affected?

- A. Data integrity
- B. Reachability
- C. Delivery confirmation
- D. Routing

**Q5.** What is the purpose of a checksum?

- A. To confirm the destination host is online
- B. To help detect possible data corruption
- C. To guarantee that every error is detected
- D. To confirm the application processed the data

**Q6.** RFC stands for:

- A. Required Format Candidate
- B. Remote File Control
- C. Request for Comments
- D. Routing Function Catalog

**Q7.** According to the lecture, the first RFC was titled:

- A. Host Software
- B. Network Control
- C. Packet Routing
- D. Internet Standards

**Q8.** According to the lecture, who published the first RFC?

- A. Vint Cerf
- B. Bob Kahn
- C. Tim Berners-Lee
- D. Steve Crocker

**Q9.** Which list matches the four RFC types given in the lecture?

- A. Standards, Draft, Informational, Historical
- B. Standards, Experimental, Informational, Historical
- C. Standards, Experimental, Commercial, Historical
- D. Proposed, Experimental, Informational, Obsolete

**Q10.** Which statement about RFCs is correct?

- A. Every RFC is an Internet Standard
- B. Only the first RFC was an informational document
- C. RFCs include several types, such as Standards, Experimental, Informational, and Historical
- D. RFCs are used only to confirm message delivery

### 12.2 Short-Answer Questions

**Section G**

1. What is the difference between reachability and delivery confirmation?
2. What does a TCP ACK indicate, and what does it not necessarily prove?
3. What is data integrity, and how can a checksum help?

**Section H**

4. What is the full form of RFC?
5. What was the title of the first RFC, and who published it?
6. List the four RFC types given in the lecture.

### 12.3 Scenario-Based Questions

**Section G**

1. H3 sends a ping to H5 and receives no reply. H3 concludes that H5 is definitely disconnected. Is this conclusion justified?
2. H3 learns that its message was received by H5, but is unsure whether the message arrived in the same form as sent. Which concept is H3 concerned about, and how does it differ from delivery?

**Section H**

3. A student finds a document titled "Host Software". What is it according to the lecture, and who published it?
4. A document is described as providing technical information rather than defining a standard. Which of the four RFC types does this most closely match? (Additional Understanding)

### 12.4 Answer Key

**MCQ answers**

| Q1 | Q2 | Q3 | Q4 | Q5 | Q6 | Q7 | Q8 | Q9 | Q10 |
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| B | C | D | A | B | C | A | D | B | C |

**Explanations**

| Q | Explanation |
|:--:|---|
| 1 | The first lecture question asks how H3 knows H5 is connected and reachable by H1. |
| 2 | Learning whether data was received is delivery confirmation. |
| 3 | A TCP ACK acknowledges specified TCP data at the receiving TCP endpoint. It does not prove application processing, a perfect cable, or continued connectivity. |
| 4 | Delivery and correctness are separate. Differing contents concern data integrity. |
| 5 | A checksum helps detect possible corruption. It does not guarantee detection of every error. |
| 6 | RFC stands for Request for Comments. |
| 7 | The first RFC was titled "Host Software". |
| 8 | The first RFC was published by Steve Crocker. |
| 9 | The lecture lists Standards, Experimental, Informational, and Historical. |
| 10 | The lecture lists several types, so not every RFC is an Internet Standard. |

**Short-answer key**

1. Reachability asks whether a destination can be reached. Delivery confirmation asks whether data was received.
2. It indicates that specified TCP data was acknowledged at the receiving TCP endpoint. It does not necessarily prove that the receiving application processed the message.
3. Data integrity is whether received data remains correct. A checksum helps detect possible corruption.
4. Request for Comments.
5. "Host Software"; published by Steve Crocker.
6. Standards, Experimental, Informational, Historical.

**Scenario key**

1. No. A failed ping does not always prove a host is disconnected.
2. Data integrity. Delivery concerns whether the data was received; integrity concerns whether it remained correct.
3. It is the first RFC, published by Steve Crocker.
4. Informational.

---

## 13. Quick Revision

### Section G Quick Revision

**The lecture's three questions**

1. How does H3 understand that H5 is still connected to the network and that H1 can reach H5?
2. How does H3 understand that the message was indeed delivered?
3. Did the message reach as H3 sent it?

| Concept | Question |
|---|---|
| Reachability | Can the destination be reached? |
| Delivery confirmation | Was the data received? |
| Data integrity | Did the data remain correct? |

These are different concepts. A destination can be reachable while a particular message is not confirmed, and a message can be delivered while its contents are incorrect.

```text
Reachability  -->  Can H5 be reached?
Delivery      -->  Was the message received?
Integrity     -->  Was the message unchanged?
```

### Section H Quick Revision

- **Full form:** RFC — Request for Comments.
- **First RFC title:** "Host Software".
- **Author of the first RFC:** Steve Crocker.
- **Four types:** Standards, Experimental, Informational, Historical.
- **RFC Count:** the lecture has an RFC Count slide; its value is not verified in these notes.

---

## 14. Knowledge Check

**Section G**

1. Which concept asks whether a destination can be reached?
2. True or false: a TCP ACK proves that the receiving application has processed the message.
3. Can a message be delivered but still be incorrect?
4. Does a passing checksum guarantee that no error occurred?
5. Why does a failed ping not always prove a host is disconnected?

**Section H**

6. Expand the abbreviation RFC.
7. Who published the first RFC?
8. What was the title of the first RFC?
9. Which RFC type is listed after Informational in the lecture?
10. Can the RFC count be stated from these notes? Explain.

### Answer Key

| Q | Answer |
|:--:|---|
| 1 | Reachability |
| 2 | False; an ACK concerns acknowledged TCP data, not application processing |
| 3 | Yes; delivery and correctness are separate |
| 4 | No; a checksum cannot guarantee detection of every error |
| 5 | The host may be reachable but not responding to the probe |
| 6 | Request for Comments |
| 7 | Steve Crocker |
| 8 | "Host Software" |
| 9 | Historical |
| 10 | No; the lecture has an RFC Count slide, but its value cannot be verified from the available material |
