Network Security

<div style="border-left: 4px solid #3b82f6; padding: 14px; margin: 16px 0; background: #0f172a;">

<strong>Topic</strong><br><br>

This note covers the Network Security section of the current HTB Information Security learning material. It builds the foundation for understanding how networks, connected systems, and data are protected using multiple security controls.

</div>

Exercise Objective

This section is intended to build an understanding of:

What Network Security is

Why Network Security is required

How different security controls work together

The purpose of firewalls

The difference between IDS and IPS

The purpose of VPNs

Access control and authorization

Encryption

Network Security responsibilities

The role of penetration testers

Red Team, Blue Team, and Purple Team perspectives

Why a firewall alone is not sufficient

How modern technologies increase attack surface

The goal is not to memorize individual definitions. The important skill is to understand how these controls fit together into a layered security architecture.

Concept Behind the Exercise

What is Network Security?

Network Security is the practice of protecting a computer network, connected systems, and data moving across the network from unauthorized access, attacks, misuse, and disruption.

A simple model is:

Users
  |
  v
Network
  |
  v
Security Controls
  |
  v
Servers / Applications
  |
  v
Data

Network Security is one part of the broader Information Security field.

Information Security
        |
        +---- Network Security
        |
        +---- Application Security
        |
        +---- Cloud Security
        |
        +---- Other Security Areas

<div style="border-left: 4px solid #3b82f6; padding: 14px; margin: 16px 0; background: #0f172a;">

<strong>Core Concept</strong><br><br>

Network Security is not a single product. It is a layered approach that combines traffic filtering, detection, prevention, secure communication, access control, encryption, monitoring, response, and recovery.

</div>

Why Network Security Matters

Organizations depend on networks for:

Internal communication

Internet access

Applications

Databases

Cloud services

Remote access

Business operations

Data transfer

A successful network compromise can result in:

Unauthorized access

Data exposure

Data modification

Service disruption

Financial loss

Reputation damage

Legal or regulatory consequences

Operational downtime

Modern environments also contain more connected systems than traditional office networks.

Examples include:

Cloud systems

Remote users

Mobile devices

IoT devices

Internet-facing applications

Third-party services

More connected assets can create a larger attack surface.

Mental Model

A useful way to think about Network Security is:

                NETWORK SECURITY
                       |
        +--------------+--------------+
        |              |              |
     Prevent         Detect        Protect
        |              |              |
    Firewall        IDS / IPS     Encryption
        |              |              |
        +--------------+--------------+
                       |
                Access Control
                       |
                       v
                 Monitoring
                       |
                       v
               Incident Response
                       |
                       v
                    Recovery

Another way to understand the overall security lifecycle is:

Identify Assets
      |
      v
Understand Risks
      |
      v
Apply Security Controls
      |
      v
Monitor
      |
      v
Detect
      |
      v
Respond
      |
      v
Recover
      |
      v
Improve

Network Security Components

The main controls covered in this section are:

Component

Main Purpose

Firewall

Controls and filters network traffic

IDS

Detects suspicious activity and generates alerts

IPS

Detects suspicious activity and can take preventive action

VPN

Provides protected connectivity over public networks

Access Control

Determines who can access resources and what they can do

Encryption

Protects information from unauthorized reading

These controls solve different security problems and are stronger when used together.

Firewall

What is a Firewall?

A firewall is a security control that filters incoming and outgoing network traffic according to predefined rules.

A simplified architecture is:

Internet
   |
   v
Firewall
   |
   v
Internal Network

The firewall acts as a traffic control point between network zones.

How Does It Work?

A firewall evaluates traffic against configured rules.

For example:

HTTPS / 443  -> ALLOW
Unwanted     -> BLOCK

The actual rules depend on the organization's security requirements and network architecture.

Simple Mental Model

Incoming Traffic
      |
      v
   Firewall
      |
      +---- Allowed ------> Destination
      |
      +---- Blocked ------> Dropped / Rejected

Why Does It Matter?

A firewall can reduce exposure by preventing unwanted traffic from reaching protected systems.

It can help enforce network boundaries and reduce the number of services directly reachable from untrusted networks.

Important Limitation

A firewall is not complete security.

An attacker may use traffic that is allowed through the firewall. Vulnerabilities can also exist at the application or other layers.

For example:

Internet
   |
   v
Firewall
   |
   v
Allowed Web Traffic
   |
   v
Vulnerable Application
   |
   v
Potential Compromise

<div style="border-left: 4px solid #ef4444; padding: 14px; margin: 16px 0; background: #0f172a;">

<strong>Security Risk</strong><br><br>

Treating a firewall as the complete security solution can create a false sense of security. A firewall should be one layer of a broader defense strategy.

</div>

IDS — Intrusion Detection System

What is an IDS?

An Intrusion Detection System monitors activity for suspicious behavior and generates alerts.

Network Traffic
      |
      v
     IDS
      |
      v
Suspicious Activity?
      |
      v
    Alert

Memory

IDS = Detect + Alert

How Does It Help?

An IDS provides visibility into suspicious activity.

For example:

Normal Activity
      |
      v
Unusual Activity
      |
      v
IDS Detection
      |
      v
Security Alert
      |
      v
Analyst Investigation

The alert itself is not necessarily proof that an attack succeeded. It is information that may require investigation.

IPS — Intrusion Prevention System

What is an IPS?

An Intrusion Prevention System can detect suspicious activity and take automated preventive action.

Network Traffic
      |
      v
     IPS
      |
      v
Suspicious Activity
      |
      v
Preventive Action

Memory

IPS = Detect + Potentially Prevent

IDS vs IPS

IDS

IPS

Detects suspicious activity

Detects suspicious activity

Primarily generates alerts

Can take preventive action

Useful for visibility and investigation

Useful for detection and active prevention

Does not primarily act as a blocking control

May block or otherwise prevent suspicious traffic

A simple memory model:

IDS -> "I saw it."
IPS -> "I saw it and can act."

VPN

What is a VPN?

A Virtual Private Network can provide a protected connection over a public network.

A common use case is remote access.

Employee
   |
   v
Internet
   |
   v
VPN
   |
   v
Company Network
   |
   v
Internal Resources

Encryption can be used to protect communication.

Why is a VPN Useful?

A VPN can protect communication when users are connecting across a public or untrusted network.

A common organizational scenario is:

Remote Employee
       |
       v
   Public Internet
       |
       v
       VPN
       |
       v
Internal Resources

Important Limitation

A VPN does not automatically make the destination system or application secure.

For example:

VPN Connection
      |
      v
Internal Server
      |
      v
Vulnerable Application

The communication may be protected while the application itself can still contain vulnerabilities.

Access Control

What is Access Control?

Access control determines:

Who can access a resource and what they are allowed to do.

Example:

Employee
 |
 +---- Email              -> Allowed
 |
 +---- HR Portal          -> Allowed
 |
 +---- Server Admin       -> Not Allowed

Access control is closely related to authentication and authorization.

Authentication vs Authorization

These concepts should not be confused.

Authentication

Authorization

Verifies identity

Determines permissions

"Who are you?"

"What can you do?"

Happens when identity is established

Determines access after identity is known

Example

User
 |
 v
Login
 |
 v
Authentication
 |
 v
Identity Verified
 |
 v
Authorization
 |
 +---- Email Access          -> Allowed
 |
 +---- Server Administration -> Not Allowed

<div style="border-left: 4px solid #eab308; padding: 14px; margin: 16px 0; background: #0f172a;">

<strong>Common Mistake</strong><br><br>

A successful login does not mean the user automatically has permission to perform every action.

<strong>Remember:</strong> Authentication establishes identity; authorization determines permissions.

</div>

Encryption

What is Encryption?

Encryption transforms information into a protected form so unauthorized parties cannot easily understand it.

It can be used to protect data in different states.

Data in Transit

Data is moving between systems.

Laptop
  |
  v
Network
  |
  v
Server

Encryption can protect the communication while the data travels.

Data at Rest

Data is stored on a system.

Examples:

Database
Hard Drive
Backup

Encryption can help protect stored information if an unauthorized party gains access to the underlying storage.

<div style="border-left: 4px solid #a855f7; padding: 14px; margin: 16px 0; background: #0f172a;">

<strong>Deep Dive</strong><br><br>

Encryption is primarily associated with protecting confidentiality. Cryptographic mechanisms can also support integrity and authenticity depending on how they are designed and used.

</div>

Defense in Depth

What is Defense in Depth?

Defense in depth means using multiple security layers instead of relying on one control.

A layered architecture can look like:

                 External Traffic
                        |
                        v
                    Firewall
                        |
                        v
                    IDS / IPS
                        |
                        v
                  Access Control
                        |
                        v
                    Encryption
                        |
                        v
                    Monitoring
                        |
                        v
                Incident Response
                        |
                        v
                     Recovery

If one control fails, another layer may still:

Prevent the attack

Detect suspicious activity

Limit the impact

Support investigation

Support recovery

<div style="border-left: 4px solid #a855f7; padding: 14px; margin: 16px 0; background: #0f172a;">

<strong>Deep Dive</strong><br><br>

The important security question is not "Which one tool protects the network?" but "How many independent opportunities exist to prevent, detect, contain, and recover from an attack?"

</div>

Network Security and the CIA Triad

Network Security controls can support the CIA objectives in different ways.

Security Objective

Example Network Security Controls

Confidentiality

Encryption, access control, VPN

Integrity

Access control, cryptographic mechanisms, monitoring

Availability

Redundancy, monitoring, defensive controls, recovery mechanisms

The exact protection depends on how each control is configured and used.

Network Security Responsibilities

Network Security is not normally the responsibility of a single person.

Different roles can contribute.

Role

Typical Contribution

CISO

Overall security strategy and risk direction

Network Security Team

Network security architecture and controls

Network Administrator

Network configuration and day-to-day operations

Security Analyst

Monitoring and investigation

Penetration Tester

Authorized security testing

Compliance Team

Regulatory and security requirement alignment

Risk Management

Risk evaluation and prioritization

IT Management

Infrastructure and resource decisions

Penetration Testing Perspective

Network Security must be tested, not simply assumed to work.

A penetration tester can simulate authorized attacks to identify weaknesses.

A useful assessment flow is:

Authorized Attack Simulation
          |
          v
    Identify Weakness
          |
          v
      Collect Evidence
          |
          v
        Report
          |
          v
   Remediation Advice
          |
          v
        Retest

Example Finding

Finding:
Unnecessary network service exposed

Risk:
Additional attack surface

Recommendation:
Restrict access or disable the service if it is not required

The value of penetration testing is not just discovering a weakness. The result should help the organization understand the risk and improve its security controls.

Red Team Perspective

A Red Team simulates adversary behavior in an authorized environment.

The objective is to test how well an organization can withstand realistic attack techniques.

A Red Team may focus on:

Attack paths

Exposed services

Security weaknesses

Security control bypasses

Evidence collection

Demonstrating impact

The work must be authorized.

Blue Team Perspective

A Blue Team focuses on defense.

Typical activities include:

Monitoring

Alert investigation

Log analysis

Threat detection

Incident response

Containment

Recovery

Security improvement

Example:

Suspicious Login
      |
      v
Security Alert
      |
      v
Security Analyst
      |
      v
Investigation
      |
      v
Containment
      |
      v
Remediation

Purple Team Perspective

Purple Team activities connect offensive and defensive knowledge.

Red Team
   |
   v
Attack Technique
   |
   v
Blue Team
   |
   v
Detection / Prevention
   |
   v
Security Improvement

The objective is to use realistic attack activity to improve defensive detection and prevention capabilities.

Attack Surface

What is the Attack Surface?

The attack surface is the collection of systems, services, interfaces, and other accessible points that could potentially be targeted.

Modern environments can contain:

Cloud systems

Remote employees

Mobile devices

IoT devices

Internet-facing applications

Internal infrastructure

Third-party services

Traditional Environment

Office
  |
  v
Internal Network
  |
  v
Servers

Modern Environment

                 Cloud
                   |
                   |
Office ---- Internal Network ---- Servers
   |                 |
   |                IoT
   |
Remote Users
   |
Mobile Devices
   |
Internet-facing Services

More connected assets can create a larger attack surface.

Why a Larger Attack Surface Matters

Every additional connected service or device can introduce:

Another configuration point

Another identity

Another interface

Another potential vulnerability

Another monitoring requirement

This does not mean every additional system is automatically insecure. It means the organization has more assets and interfaces that must be properly secured and monitored.

Security Lifecycle

Network Security fits into the broader Information Security lifecycle.

Risk Assessment
       |
       v
Security Planning
       |
       v
Security Controls
       |
       v
Monitoring & Detection
       |
       v
Incident Response
       |
       v
Disaster Recovery
       |
       v
Continuous Improvement
       |
       +----------------------+
                              |
                              v
                       Reassess Risk

Risk Assessment

Risk assessment identifies and evaluates potential threats and vulnerabilities.

Example:

Internet-facing Server
        |
        v
Outdated Software
        |
        v
Known Weakness
        |
        v
Risk Assessment
        |
        v
Prioritize Remediation

Important questions include:

What can go wrong?

What weakness exists?

How likely is exploitation?

What could the impact be?

Which risks should be addressed first?

Security Planning

After risks are identified, security teams determine how those risks should be addressed.

Example:

Risk:
Outdated public server

Plan:
Patch
  |
  v
Test
  |
  v
Deploy
  |
  v
Verify
  |
  v
Monitor

Planning can include:

Policies

Procedures

Security objectives

Resource allocation

Risk treatment

Security Controls

Security controls turn security plans into practical protection.

Example:

Risk:
Unauthorized Account Access

Controls:
    |
    +---- MFA
    |
    +---- Access Control
    |
    +---- Monitoring

Controls may be used to:

Prevent incidents

Detect incidents

Reduce impact

Support response

Support recovery

Monitoring and Detection

Security monitoring provides visibility into activity across the environment.

Example:

Failed Login Attempts
        |
        v
        5
        |
        v
       20
        |
        v
       50
        |
        v
   Security Alert

Common technologies include:

SIEM

IDS

Log analysis

Security monitoring platforms

Monitoring is particularly important because prevention is not guaranteed.

Incident Response

A simplified incident response process is:

Detect
  |
  v
Investigate
  |
  v
Contain
  |
  v
Eradicate
  |
  v
Recover
  |
  v
Learn

The purpose is to limit damage, restore normal operations, and learn from the incident.

Disaster Recovery

Disaster recovery focuses on restoring systems and data after major disruption.

Major Incident
      |
      v
Production Unavailable
      |
      v
Backup / Recovery Systems
      |
      v
Restore
      |
      v
Business Resumes

The main objective is to reduce downtime, data loss, and business impact.

Continuous Improvement

Security controls should be reviewed and improved over time.

Incident
   |
   v
Investigation
   |
   v
Lessons Learned
   |
   v
Improve Controls
   |
   v
Better Security
   |
   +----------------------+
                          |
                          v
                    Future Events

A security incident should therefore produce useful lessons for future security improvements.

Practical Security Reasoning

When analyzing a network, avoid asking only:

Is there a firewall?

Instead, ask:

What assets exist?
       |
       v
What is exposed?
       |
       v
What services are available?
       |
       v
Who can access them?
       |
       v
What traffic is allowed?
       |
       v
What is being monitored?
       |
       v
How is suspicious activity detected?
       |
       v
How will an incident be contained?
       |
       v
How will systems be recovered?
       |
       v
What should be improved afterward?

This mindset is more useful than memorizing individual security tools.

Security Tool Overview

Tool / Technology

Main Purpose

Firewall

Network traffic filtering

IDS

Suspicious activity detection

IPS

Detection and preventive action

VPN

Protected network connectivity

SIEM

Security event collection and analysis

Vulnerability Scanner

Identifying potential weaknesses

Nmap

Network discovery and service scanning

Wireshark

Network traffic and protocol analysis

Metasploit

Exploitation framework for authorized testing

Burp Suite

Web application security testing

John the Ripper

Password/hash cracking

<div style="border-left: 4px solid #ef4444; padding: 14px; margin: 16px 0; background: #0f172a;">

<strong>Security Rule</strong><br><br>

Security testing tools should only be used against systems and environments for which explicit authorization exists.

</div>

Tool Selection Mindset

Do not start with:

Which tool should I run?

Start with:

What security question am I trying to answer?

Then:

Security Question
       |
       v
Information Required
       |
       v
Choose Appropriate Tool
       |
       v
Collect Evidence
       |
       v
Interpret Result
       |
       v
Security Decision

The tool itself is only one part of the process.

Common Mistakes

Mistake 1: Treating a Firewall as Complete Security

Why it happens

A firewall is often viewed as the main security boundary.

Why it is wrong

A firewall primarily filters traffic according to rules.

It does not guarantee:

Secure applications

Safe credentials

Secure configurations

Absence of vulnerabilities

Absence of malicious activity in allowed traffic

Correct approach

Use defense in depth.

Firewall
+
IDS / IPS
+
Access Control
+
Encryption
+
Monitoring
+
Incident Response

Lesson

A firewall is a layer, not the entire security architecture.

Mistake 2: Confusing Authentication and Authorization

Wrong mental model

Successful Login
       |
       v
User Can Do Everything

Correct mental model

Authentication
      |
      v
Who are you?
      |
      v
Authorization
      |
      v
What can you do?

Lesson

Authentication verifies identity.

Authorization determines permissions.

Mistake 3: Treating CIA as Three Tools

Confidentiality, Integrity, and Availability are security objectives.

They are not tools.

For example:

Security Objective
        |
        v
Confidentiality
        |
        v
Encryption + Access Control + Authentication

The controls help achieve the objective.

Mistake 4: Assuming a VPN Makes the Destination Secure

A VPN can protect the connection while the destination application can still contain vulnerabilities.

VPN Connection
      |
      v
Internal Server
      |
      v
Vulnerable Application

Lesson

Protecting the communication path does not automatically secure the endpoint or application.

Mistake 5: Thinking Security Ends After Deployment

Security is continuous.

Assess
  |
  v
Plan
  |
  v
Implement
  |
  v
Monitor
  |
  v
Respond
  |
  v
Recover
  |
  v
Improve
  |
  +-----> Assess Again

Important Distinctions

Concept A

Concept B

Main Difference

Authentication

Authorization

Identity verification vs permissions

IDS

IPS

Detection/alerting vs detection with preventive action

Confidentiality

Privacy

Unauthorized disclosure protection vs appropriate handling of personal information

Firewall

IDS

Traffic filtering vs suspicious activity detection

Network Security

Information Security

Network-focused protection vs broader information protection

Red Team

Blue Team

Authorized offensive simulation vs defensive operations

Network Security and Information Security

Network Security is a part of Information Security.

Information Security
        |
        +---- Network Security
        |
        +---- Application Security
        |
        +---- Cloud Security
        |
        +---- Incident Response
        |
        +---- Risk Management
        |
        +---- Security Engineering

Information Security

Broader discipline concerned with protecting information and systems.

Network Security

Focused specifically on protecting network infrastructure, communications, connected systems, and related access paths.

Attacker Perspective

An attacker may attempt to:

Discover exposed services

Obtain unauthorized information

Modify information

Disrupt availability

Bypass access controls

Exploit vulnerable applications

Abuse exposed network services

Take advantage of weak configurations

The exact technique depends on the environment and vulnerability.

Defender Perspective

A defender should continuously ask:

What assets exist?
       |
       v
Who should access them?
       |
       v
What traffic should be allowed?
       |
       v
What should be monitored?
       |
       v
What behavior is abnormal?
       |
       v
How will an incident be contained?
       |
       v
How will systems be recovered?
       |
       v
What should change afterward?

Real-World Connection

Security Area

Relevant Knowledge

SOC / Blue Team

Monitoring, IDS, SIEM, incident response

Network Security

Firewalls, IDS/IPS, VPN, access control

Penetration Testing

Network discovery, exposed services, authorized security testing

Security Engineering

Architecture, controls, defense in depth

Incident Response

Detection, containment, eradication, recovery

Risk Management

Risk identification and prioritization

Compliance

Security requirements and controls

Practical Knowledge

The most reusable practical lesson from this section is to think in terms of security questions and evidence.

Instead of memorizing:

Firewall
IDS
IPS
VPN

connect each technology to the problem it solves.

Need to control traffic
        |
        v
     Firewall

Need to detect suspicious activity
        |
        v
        IDS

Need to detect and potentially prevent
        |
        v
        IPS

Need protected connectivity over a public network
        |
        v
        VPN

Need to restrict who can access resources
        |
        v
   Access Control

Need to protect information from unauthorized reading
        |
        v
    Encryption

This makes the concepts easier to apply in future labs.

Screenshot Placeholders

Screenshot Placeholder

Add screenshot showing the HTB Network Security section here.

Screenshot Placeholder

Add a personal diagram showing Firewall, IDS/IPS, VPN, Access Control, and Encryption.

Screenshot Placeholder

Add an authorized lab screenshot when practical Network Security testing is completed.

Additional Knowledge

The concepts in this topic were reinforced using different explanations and real-world examples rather than simply translating or repeating the source wording.

The learning method used was:

HTB Concept
     |
     v
Simple Explanation
     |
     v
Different Real-World Example
     |
     v
Security Perspective
     |
     v
Practical Connection
     |
     v
Interview Understanding

The separate Python and book-related exercise discussed in the conversation is not included as part of this Network Security topic because it belongs to a different learning area.

Deeper Technical Understanding

Why Network Security Uses Multiple Controls

Consider a network protected only by a firewall:

Internet
   |
   v
Firewall
   |
   v
Internal Network

The firewall may block unwanted traffic, but permitted traffic can still reach a vulnerable service.

With additional controls:

Internet
   |
   v
Firewall
   |
   v
IDS / IPS
   |
   v
Access Control
   |
   v
Application
   |
   v
Monitoring
   |
   v
Incident Response

The organization now has multiple opportunities to:

Prevent attacks

Detect attacks

Investigate events

Contain incidents

Recover systems

Improve defenses

This is the practical meaning of defense in depth.

End-to-End Security Model

The concepts from this section can be connected into a complete workflow:

Asset
  |
  v
Attack Surface
  |
  v
Risk Assessment
  |
  v
Security Planning
  |
  v
Security Controls
  |
  +---- Firewall
  |
  +---- IDS / IPS
  |
  +---- Access Control
  |
  +---- Encryption
  |
  +---- VPN
  |
  v
Monitoring
  |
  v
Detection
  |
  v
Incident Response
  |
  v
Recovery
  |
  v
Continuous Improvement

This is the connection between foundational Information Security theory and practical security operations.

Quick Reference

Item

Key Information

Network Security

Protect networks, connected systems, and network communications

Firewall

Filter/control traffic

IDS

Detect suspicious activity and alert

IPS

Detect suspicious activity and potentially prevent it

VPN

Provide protected connectivity over a public network

Access Control

Determine who can access resources and what they can do

Encryption

Protect information from unauthorized reading

Attack Surface

Collection of accessible points that could potentially be targeted

Defense in Depth

Use multiple independent security layers

Red Team

Authorized offensive security simulation

Blue Team

Defensive monitoring and response

Purple Team

Connect offensive testing with defensive improvement

SIEM

Collect and analyze security events

Nmap

Network discovery and service scanning

Wireshark

Network traffic and protocol analysis

One-Minute Revision

Network Security
    -> Protect networks, connected systems, and data in transit.

Firewall
    -> Filter network traffic.

IDS
    -> Detect suspicious activity and alert.

IPS
    -> Detect suspicious activity and potentially prevent it.

VPN
    -> Provide protected connectivity over a public network.

Access Control
    -> Decide who can access what and what they can do.

Encryption
    -> Protect information from unauthorized reading.

Defense in Depth
    -> Do not depend on a single security control.

Attack Surface
    -> More connected assets can create more potential points of exposure.

Red Team
    -> Authorized offensive simulation.

Blue Team
    -> Defensive monitoring and response.

Purple Team
    -> Use offensive activity to improve defensive capabilities.

Security Lifecycle
    -> Assess -> Plan -> Implement -> Monitor -> Respond -> Recover -> Improve.

Knowledge Check

Answer these without looking back at the notes.

Why is a firewall alone insufficient to secure an entire network?

What is the difference between authentication and authorization?

What is the main difference between an IDS and an IPS?

Why can a VPN protect communication without making the destination application secure?

How does defense in depth reduce the impact of a failed security control?

Why can cloud systems, remote users, IoT devices, and Internet-facing applications increase an organization's attack surface?

How do Network Security controls fit into the broader Information Security lifecycle?
