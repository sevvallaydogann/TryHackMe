# RootMe - Manual Web Exploitation & Privilege Escalation

**Category:** Web Security | **Method:** CLI / cURL (No GUI) | **Language:** PHP & Python

This challenge represents a comprehensive penetration testing scenario focusing on two critical vulnerabilities: arbitrary file upload and SUID misconfiguration. Unlike standard approaches that rely on browser interactions, I conducted this exploitation entirely through the command line. This constraint required a deeper understanding of HTTP requests and manual payload delivery using `curl`, rather than relying on graphical interface tools.

## Walkthrough

## Part 1: 

Every web challenge starts with finding where the vulnerability hides. A standard port scan showed me that an Apache server was running, but the homepage was just a static landing page. There was nothing to hack there.

I needed to see what the developers were hiding. I used **Gobuster** to brute-force the directory structure. My goal was to find administrative panels or backup files that aren't linked in the menu.

The scan revealed a critical path: `/panel/`.

When I inspected this endpoint (using `curl`), it turned out to be a file upload page and if you can upload a file, you can execute code.

## Part 2: 

My plan was uploading a PHP script that allows me to run system commands. However, the server had a security filter in place. When I tried to upload a standard `shell.php` file, the server rejected it immediately. The developers had blocked the `.php` extension. 

Apache servers are often configured to execute code in files with alternative extensions. I renamed my payload to **`shell.phtml`**. The server's filter only looked for `.php`, so it let the `.phtml` file through, but the Apache engine still executed it as code.

## Part 3: 

Since I was working from the CLI, setting up a reverse shell listener and keeping a connection alive was cumbersome. Instead, I opted for a stateless **Web Shell**.

I created a tiny, one-line script:
```php
<?php system($_GET["cmd"]); ?>
```
This script doesn't connect back to me. Instead, it waits for me to send a command via the URL (e.g., ?cmd=ls). It turns the web server into a command-line interface.

I uploaded this file using a crafted curl POST request. Once uploaded, I could interact with the server by simply sending GET requests to my uploaded file. I successfully located and read the user.txt flag without ever leaving my terminal.

## Part 4:

Last but not least, I needed to become "root" (the administrator). To do this, I looked for SUID (Set User ID) binaries.

Why SUID? In Linux, if a file has the SUID bit set, it executes with the permissions of the file's owner, not the user who runs it. If the owner is Root, and the program has a flaw, you can become Root.

I ran a search for all SUID files owned by root. Buried among the standard system tools was an anomaly: /usr/bin/python.

The Exploit: Python is a programming language that can interface with the operating system. Giving it SUID permissions is a massive security mistake. Since Python was running as root, I didn't need a password or an exploit script. I simply told Python to open the protected administrative flag: ```bash /usr/bin/python -c 'print(open("/root/root.txt").read())```
Because the binary had SUID permissions, the system treated this command as if the Administrator themselves typed it. It printed the root flag to my terminal, completing the challenge.




