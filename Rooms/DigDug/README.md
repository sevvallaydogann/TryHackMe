# Dig Dug - DNS Analysis & Exploitation

**Platform:** TryHackMe | **Topic:** Network Security / DNS | **Tool:** `dig`, `nmap`

## Introduction & Learning Outcomes
This project focuses on the **Domain Name System (DNS)**, often referred to as the "phonebook of the internet." The goal is not just to capture the flag, but to understand how DNS queries work, the difference between UDP and TCP in DNS, and how to enumerate records manually.

### Core Concepts Covered:
* **DNS Protocol:** How domain names are translated into IP addresses.
* **Transport Layer:** Why DNS uses **UDP** for queries and **TCP** for zone transfers.
* **Record Types:** Understanding `A`, `TXT`, and `PTR` records.

---

## Technical Background 

Before diving into the solution, it is essential to understand the underlying technology.

### Why Port 53? (UDP vs. TCP)
DNS servers typically listen on **Port 53**.
* **UDP Port 53:** Used for standard queries (e.g., "What is the IP of google.com?"). It is connectionless and fast.
* **TCP Port 53:** Used for **Zone Transfers (AXFR)** or when the response size exceeds 512 bytes.
* *Security Note:* A standard Nmap scan (`nmap -sC -sV`) only checks TCP ports. To find a DNS server, we must explicitly scan UDP.

### Key DNS Record Types
* **A Record:** Maps a hostname to an IPv4 address.
* **PTR Record:** Maps an IP address back to a hostname (Reverse DNS).
* **TXT Record:** Arbitrary text data, often used for verification (SPF, DKIM) or leaving notes (where we found the flag).

---

## Walkthrough 

### Discovery 
A standard scan showed only SSH open. Knowing this is a DNS challenge, I performed a targeted **UDP Scan**:

```bash
sudo nmap -sU -p 53 <TARGET_IP>
```

Port 53/udp was open. This confirmed a DNS service (Bind9) was running.

### Interacting with DNS (dig command)
Instead of a browser, I used the dig (Domain Information Groper) tool to talk directly to the server. 

Firstly, I tried to find the domain name associated with the IP: dig -x <TARGET_IP> @<TARGET_IP> 
There was no answer. The server is not configured for reverse lookups. 
Then, following CTF conventions, I queried for the domain givemetheflag.com: dig @<TARGET_IP> givemetheflag.com
I queried the authoritative nameserver specifically for this domain.

### The Response
```bash
;; ANSWER SECTION:
givemetheflag.com.      0       IN      TXT     "flag{0767ccd06e79853318f25aeb08ff83e2}"
```

Why TXT? Because administrators often use TXT records to store descriptive text. In this CTF, it was used to hide the flag.

For the last step I attempted a Zone Transfer (AXFR) to replicate the entire DNS database: dig axfr @<TARGET_IP> givemetheflag.com 
"Connection Timed Out" The server correctly blocks TCP Port 53 or AXFR requests, preventing data leakage. 
