# 🥒 Pickle Rick - CTF Writeup

**Difficulty:** Easy | **Language:** Python & Bash

## Project Overview
In this room, I exploited a vulnerable web server to retrieve 3 secret ingredients (flags). Instead of relying solely on the web interface, I developed a custom **Python Exploit Tool** (`exploit.py`) to automate the interaction with the command panel.

## Tools Used
* **Nmap:** Network scanning and service enumeration.
* **Python (Requests & BeautifulSoup):** Automating authentication and command execution.
* **Linux Terminal:** Manual enumeration and privilege escalation.

## Walkthrough

### 1. Reconnaissance
I started with an Nmap scan and identified an Apache web server running on **port 80**.
* **Finding 1:** Analyzed the HTML source code and discovered a hidden username: `R1ckRul3s`.
* **Finding 2:** Checked `robots.txt` and found a suspicious string, which turned out to be the password: `Wubbalubbadubdub`.

### 2. Exploitation (Custom Script)
Upon logging in, I accessed a "Command Panel". To streamline the process, I wrote a Python script (`exploit.py`) that:
1.  Logs in automatically using the discovered credentials.
2.  Maintains a persistent session.
3.  Allows me to execute system commands directly from my terminal.

**Bypass Technique:**
The `cat` command was disabled on the server. I bypassed this restriction by using alternative commands like `less`, `head`, and `nl` to read the flag files.

### 3. Privilege Escalation
After gaining initial access, I checked for sudo privileges:
`sudo -l`

**Result:**
```bash
(ALL) NOPASSWD: ALL
```
I discovered that the user could run any command as root without a password. I executed sudo ls /root to access the file system and retrieved the final ingredient.


**This project was created for educational purposes.**
