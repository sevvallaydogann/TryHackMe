# RootMe - Manual Web Exploitation & Privilege Escalation

**Category:** Web Security | **Method:** CLI / cURL (No GUI) | **Language:** PHP & Python

This challenge represents a comprehensive penetration testing scenario focusing on two critical vulnerabilities: arbitrary file upload and SUID misconfiguration. Unlike standard approaches that rely on browser interactions, I conducted this exploitation entirely through the command line. This constraint required a deeper understanding of HTTP requests and manual payload delivery using `curl`, rather than relying on graphical interface tools.

## Technical Walkthrough

### Network Reconnaissance and Discovery
The investigation began with a network scan to identify the attack surface. While standard port scanning revealed an Apache web server, the critical entry point was hidden from the main navigation. By utilizing directory brute-forcing techniques, I uncovered a hidden `/panel` directory. This administrative endpoint presented a file upload form, which became the primary vector for the initial compromise.

### Bypassing Security Filters with a Web Shell
Upon attempting to upload a standard PHP reverse shell, the server rejected the file, indicating the presence of an extension-based filter. To circumvent this security measure, I leveraged a common Apache misconfiguration where alternative extensions are executed as PHP. By renaming the payload extension from `.php` to **`.phtml`**, I successfully bypassed the filter.

Instead of a heavy reverse shell connection, I deployed a lightweight **Web Shell** (`<?php system($_GET["cmd"]); ?>`). This approach allowed me to execute system commands directly via HTTP GET requests. Using `curl` to interact with this shell, I was able to traverse the file system and retrieve the user flag without needing a persistent network listener.

### Privilege Escalation via SUID Misconfiguration
With initial access established as the `www-data` user, the focus shifted to elevating privileges to root. I initiated a search for binaries with the **SUID (Set Owner User ID)** bit enabled. This special permission allows a binary to execute with the privileges of the file owner—in this case, root.

The scan identified `/usr/bin/python` as having SUID permissions. This is a severe security oversight, as Python can interface directly with the operating system. Exploiting this, I crafted a one-line Python command to read the protected `/root/root.txt` file. Since the Python process spawned with root privileges due to the SUID bit, it bypassed the standard file access controls, allowing me to retrieve the final flag and complete the system compromise.




