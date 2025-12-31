# Dig Dug - CTF Writeup

**Platform:** TryHackMe | **Difficulty:** Easy | **Category:** DNS Enumeration

## Overview
"Dig Dug" is a capture-the-flag challenge focusing on **DNS (Domain Name System)** exploration. The goal was to uncover a hidden flag stored within the DNS records of a target server using command-line tools.

## Tools Used
* **Nmap:** For initial port scanning (TCP & UDP).
* **Dig (Domain Information Groper):** For querying DNS records and performing zone transfers.

## Walkthrough

### 1. Reconnaissance
I started with a standard Nmap scan (`-sC -sV`) which revealed only SSH (Port 22). Suspecting a DNS challenge, I specifically scanned for **UDP Port 53**:
`sudo nmap -sU -p 53 <TARGET_IP>`
* **Result:** Port 53/udp was **OPEN**.

### 2. DNS Enumeration
I attempted to interact with the DNS server using `dig`:

* **Version Query:** I tried to query `version.bind` to identify the server software, but the query was refused (ANSWER: 0).
* **Reverse Lookup:** I performed a reverse DNS lookup (`dig -x <IP>`) to find the domain name associated with the IP, but it returned no results.

### 3. Exploitation (The Flag)
Following common CTF conventions, I queried the server for the domain name `givemetheflag.com`:
```bash
dig @<TARGET_IP> givemetheflag.com
```
Result: The server responded with a TXT record containing the flag: ``` flag{0767ccd06e79853318f25aeb08ff83e2} ```

### 4. Zone Transfer Attempt
I attempted a Zone Transfer (AXFR) to retrieve all DNS records: dig axfr @<TARGET_IP> givemetheflag.com 
The request timed out.
The server has TCP Port 53 closed or firewall rules blocking zone transfers, securing it against this specific attack.
