# Module 02: Network Foundations (Part — HTTP Security & Defense)

## Overview

In this part of the module, I learned how HTTP requests work from a security perspective and how systems defend against attacks.

This section focused on how attackers manipulate requests and how proper validation and filtering can prevent exploitation.
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/4fd5a683-70e6-4065-95ff-b755594a27fa" />

---

## HTTP Protocol Security Basics

I understood that HTTP is a stateless protocol where each request is independent.

Each request contains:

* Method (GET, POST, etc.)
* Headers
* Optional body

The key security point is that all request data comes from the client, which means it cannot be trusted.
<img width="768" height="595" alt="image" src="https://github.com/user-attachments/assets/e7a6d360-1779-4fd2-a79a-ce0f5d578f42" />

---

## HTTP Headers and Security

I learned that headers play a major role in communication, but they are also controlled by the client.

Important headers include:

* Host
* Content-Type
* Authorization

Security headers in responses:

* HSTS
* Content Security Policy
* X-Frame-Options

These help protect users but cannot replace server-side validation.
<img width="474" height="431" alt="image" src="https://github.com/user-attachments/assets/4db2b1ae-1e85-4b44-87b5-95dba3a0534e" />

---

## Request Filtering

I learned how systems filter incoming requests:

* IP allowlisting
* WAF rules
* Rate limiting
* Input validation

The most important concept is:

* Allow valid input instead of blocking known bad input
<img width="1024" height="485" alt="image" src="https://github.com/user-attachments/assets/8cd0f6ad-8c0a-4b9e-b458-7c3a254e4ca1" />

---

## Error Handling and Information Leakage

I understood that error messages can expose sensitive information.

Examples:

* Stack traces
* Database queries
* Internal system details

Best practice:

* Show generic errors to users
* Log detailed errors internally
<img width="345" height="257" alt="image" src="https://github.com/user-attachments/assets/9c36b167-d1e0-4bbd-a83a-ead18f12f409" />

---

## URL Encoding and Security

I learned how special characters are encoded using percent encoding.

Example:

* `/` → `%2F`

Important concept:

* Input must be decoded before validation

Otherwise, attackers can bypass filters using encoded values.
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/b84f11f6-18c1-4ed8-aba6-61f40dcf6ac7" />

---

## Encoding-Based Attacks

I understood techniques like:

* Double encoding
* Path traversal using encoded characters

Defense:

* Normalize and decode input fully
* Validate after decoding
