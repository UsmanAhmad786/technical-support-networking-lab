# Networking Fundamentals & Troubleshooting

> A personal learning project focused on developing practical networking and troubleshooting skills for SaaS, cloud, VPN and Zero Trust environments.

## Overview

Understanding networking fundamentals is an important part of effective technical support.

This document covers the networking concepts I am currently developing practical knowledge in, with a focus on how they can be applied when investigating real-world technical issues.

---

## 1. IP Addresses

An IP address identifies a device or network interface.

Example:

```text
192.168.1.25
```

Common private IPv4 address ranges include:

* `10.0.0.0/8`
* `172.16.0.0/12`
* `192.168.0.0/16`

Private addresses are commonly used within internal networks and are not directly routable across the public Internet.

### Troubleshooting questions

When investigating an IP connectivity issue, I would check:

* What IP address does the device have?
* Is the address expected?
* Is the device on the correct subnet?
* Can it reach its default gateway?
* Can it reach the destination?

---

## 2. Subnets and CIDR

CIDR notation describes the size of a network.

For example:

```text
192.168.1.0/24
```

A `/24` IPv4 network contains 256 addresses, including the network and broadcast addresses.

Understanding subnetting is important when determining whether two devices can communicate directly or whether traffic needs to be routed.

---

## 3. TCP vs UDP

### TCP

TCP is connection-oriented and provides reliable, ordered delivery.

Common examples include:

* HTTPS
* SSH
* Database connections

### UDP

UDP is connectionless and has lower protocol overhead.

It is commonly used for:

* DNS
* VoIP
* Streaming
* VPN technologies such as WireGuard

When troubleshooting connectivity, identifying whether an application uses TCP or UDP can help determine whether a firewall or network policy is affecting the connection.

---

## 4. Ports

Ports allow multiple network services to operate on the same IP address.

| Service | Typical Port | Protocol |
| ------- | -----------: | -------- |
| HTTP    |           80 | TCP      |
| HTTPS   |          443 | TCP      |
| SSH     |           22 | TCP      |
| DNS     |           53 | UDP/TCP  |

A server can be reachable at the IP level while a specific service or port is unavailable.

For example:

```text
Server reachable
       ↓
TCP 443 works
       ↓
TCP 22 blocked
```

This distinction is important when troubleshooting application connectivity.

---

## 5. DNS

DNS translates human-readable hostnames into IP addresses.

Example:

```text
application.example.com
          ↓
         DNS
          ↓
      10.10.10.20
```

Useful commands include:

```bash
nslookup application.example.com
dig application.example.com
```

When a hostname fails but the IP address works, DNS becomes an important area of investigation.

---

## 6. Routing

Routing determines where network traffic should be sent.

On Linux, I can inspect routing information with:

```bash
ip route
```

Example:

```text
default via 192.168.1.1 dev eth0
```

This indicates that traffic without a more specific route will be sent through the default gateway.

When troubleshooting, I would check whether a route exists to the destination and whether traffic is being sent through the expected interface.

---

## 7. NAT

Network Address Translation allows private IP addresses to communicate through a public IP address.

A simplified example:

```text
Laptop
192.168.1.25
     |
     ↓
Home Router
     |
     ↓
Public IP
     |
     ↓
Internet
```

NAT is particularly important when troubleshooting peer-to-peer VPN connectivity because it can affect how two devices establish connections.

---

## 8. Firewalls

Firewalls control network traffic based on factors such as:

* Source IP
* Destination IP
* Protocol
* Port
* Direction

For example:

```text
TCP/443 → Allowed
TCP/22  → Blocked
UDP     → Restricted
```

When investigating a connectivity issue, I would establish whether traffic is being blocked locally, by a network firewall, or at the destination.

---

## 9. Useful Linux Networking Commands

### Network interfaces

```bash
ip addr
```

### Routing table

```bash
ip route
```

### Basic connectivity

```bash
ping <destination>
```

### DNS resolution

```bash
dig <hostname>
nslookup <hostname>
```

### HTTP troubleshooting

```bash
curl -v https://example.com
```

### Listening ports

```bash
ss -tuln
```

### Network path

```bash
traceroute <destination>
```

### Packet capture

```bash
tcpdump
```

### Service logs

```bash
journalctl
```

The objective is not simply to know these commands, but to understand what evidence they provide during troubleshooting.

---

# Troubleshooting Methodology

When investigating a technical issue, I prefer a structured, evidence-driven approach.

```text
Understand the problem
        ↓
Establish scope and impact
        ↓
Reproduce where possible
        ↓
Gather evidence
        ↓
Identify the affected layer
        ↓
Form a hypothesis
        ↓
Test the hypothesis
        ↓
Apply the appropriate fix
        ↓
Verify the original problem
        ↓
Document the resolution
```

A useful way of thinking about a connectivity problem is:

```text
Network
   ↓
IP connectivity
   ↓
Routing
   ↓
Firewall
   ↓
DNS
   ↓
TCP/UDP
   ↓
Application
```

This helps avoid making random configuration changes and allows the investigation to focus on the actual failure point.

---

# Example Troubleshooting Scenario

### Problem

A customer reports:

> "The application is unavailable."

Instead of immediately assuming the application is down, I would break the problem into smaller questions.

### Step 1 — Can the hostname resolve?

```bash
dig application.example.com
```

### Step 2 — Can the destination be reached?

```bash
ping <destination>
```

### Step 3 — Is the required port accessible?

```bash
curl -v https://application.example.com
```

or where appropriate:

```bash
nc -vz <destination> 443
```

### Step 4 — Is routing correct?

```bash
ip route
```

### Step 5 — Is traffic being blocked?

Investigate local and network firewall rules.

### Step 6 — Is the application itself healthy?

If networking is functioning correctly, move further up the stack and investigate the application, service status and logs.

---

# Key Takeaways

Effective technical troubleshooting is not about immediately trying random fixes.

It is about:

1. Understanding the expected behaviour.
2. Establishing the scope and impact.
3. Gathering evidence.
4. Breaking the problem into layers.
5. Forming a testable hypothesis.
6. Testing one change at a time.
7. Identifying the root cause.
8. Confirming the resolution.
9. Documenting what was discovered.

These principles can be applied across SaaS applications, APIs, cloud services, VPNs, networking and Zero Trust platforms.
