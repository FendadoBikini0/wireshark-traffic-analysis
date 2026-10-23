# Wireshark Lab 01 — Network Traffic Analysis

## Overview

This lab explores network traffic analysis using Wireshark. The goal was to investigate DNS resolution, identify TLS handshake information, and observe TCP communication patterns.

## Objectives

- Identify DNS queries and responses.
- Understand the difference between DNS resolution and TLS handshakes.
- Examine TCP connections and common TCP analysis warnings.
- Distinguish normal network behavior from events that require further investigation.

## Environment

- **Tool:** Wireshark
- **Operating system:** Windows
- **Capture interface:** Ethernet interface
- **Local IPv4 address:** `10.0.0.101`

## Investigation and Findings

### 1. DNS Analysis

Using the `dns` display filter, I identified A and HTTPS queries associated with `chatgpt.com`.

The A query returned the IPv4 address:

- `104.18.32.47`

I also observed a DNS query for `v10.events.data.microsoft.com`, which returned:

- `52.168.117.175`

These observations demonstrate how DNS queries can be used to investigate domain resolution during network activity.

### 2. TLS Handshake Analysis

I used the following display filter to identify TLS Client Hello messages:

```text
tls.handshake.type == 1
```

The Server Name Indication (SNI) field revealed:

- `v10.events.data.microsoft.com`

SNI can expose the requested hostname during a TLS handshake, even when the application data exchanged afterward is encrypted.

### 3. TCP Analysis

I examined TCP packets associated with `104.18.32.47`.

Observed characteristics included:

- TCP ACK packets.
- TCP Keep-Alive packets.
- Source port `443` and destination port `50246`.

I also used the following filter to investigate TCP analysis events:

```text
tcp.analysis.flags
```

The capture contained warnings such as suspected retransmissions and suspected out-of-order segments. When filtering for traffic involving `104.18.32.47`, I observed two TCP Keep-Alive packets.

These events do not, by themselves, establish malicious activity or a network fault.

## Key Learnings

- DNS resolves domain names into IP addresses and can return other types of records.
- TLS Client Hello initiates TLS negotiation and may reveal the requested hostname through SNI.
- TCP ACK and Keep-Alive packets help explain connection behavior.
- Retransmissions and out-of-order packets require contextual analysis before drawing conclusions.
- A single warning or packet is not sufficient evidence of a security incident.

## Conclusion

This lab provided practical experience analyzing DNS, TLS, and TCP traffic using Wireshark. The observations were consistent with ordinary network communication, but the capture alone was insufficient to determine the full purpose of every connection or rule out all security concerns.

## Tools and References

- [Wireshark](https://www.wireshark.org/)
- [Wireshark Display Filter Reference](https://www.wireshark.org/docs/dfref/)
- [RFC 9293 — Transmission Control Protocol](https://www.rfc-editor.org/rfc/rfc9293)
- [RFC 8446 — TLS 1.3](https://www.rfc-editor.org/rfc/rfc8446)
