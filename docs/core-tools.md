# Core Tools

These are the fundamental tools every ethical hacker and penetration tester should master. They form the foundation for reconnaissance, vulnerability analysis, exploitation, and post-exploitation.

## Burp Suite

*   **Description:** A comprehensive and highly versatile web application security testing platform. It acts as a proxy, allowing you to intercept, inspect, and modify HTTP/HTTPS traffic between your browser and a web server. Burp Suite is essential for understanding web application behavior, identifying vulnerabilities, and performing advanced attacks.  It has a free (Community) edition and a paid (Professional) edition.
*   **Key Uses:**
    *   **Intercepting and Manipulating HTTP/HTTPS Requests:** Analyze and modify GET, POST, PUT, DELETE, and other HTTP methods.  Inspect headers, cookies, and request/response bodies.
    *   **Spidering Web Applications:** Automatically map the application's structure and identify all URLs and links.
    *   **Vulnerability Scanning:**  The Professional edition includes automated vulnerability scanners for common web application flaws like SQL injection, XSS, and CSRF.
    *   **Fuzzing:**  Send a large number of modified inputs to an application to uncover injection vulnerabilities, buffer overflows, or other unexpected behavior.
    *   **Intruder:**  A powerful tool for automating custom attacks, such as brute-forcing passwords or exploiting injection vulnerabilities.
    *   **Repeater:**  Manually resend requests and analyze responses.  Useful for testing complex attack scenarios.
    *   **Decoder:** Encode and decode data in various formats (e.g., URL encoding, Base64, HTML encoding).
    *   **Extender:**  Extend Burp Suite's functionality with custom extensions (written in Java or Python).
*   **Advanced Techniques:**
    *   **Writing Custom Burp Suite Extensions:** Create extensions to automate tasks, add new vulnerability checks, or integrate with other tools.
    *   **Using Collaborator:** A Burp Suite tool that can detect out-of-band vulnerabilities (e.g., blind SQL injection, XXE).
    *   **Exploiting Advanced Injection Vulnerabilities:**  Burp Suite is invaluable for exploiting complex injection vulnerabilities that require careful manipulation of requests and responses.
*   **Resources:**
    *   [PortSwigger's Burp Suite Documentation](https://portswigger.net/burp/documentation)
    *   [Burp Suite Tutorials](https://portswigger.net/web-security/learning-path)
    *   [Burp Suite Extensions](https://portswigger.net/bappstore)

## Nmap (Network Mapper)

*   **Description:** A powerful and versatile network scanning and service discovery tool. Nmap is used to gather information about target networks and systems, identify open ports and services, and detect operating systems and software versions.  It is a command-line tool but can be used with graphical interfaces like Zenmap.
*   **Key Uses:**
    *   **Host Discovery (Ping Scanning):** Identify active hosts on a network.
    *   **Port Scanning:** Determine which ports are open on a target host and the services running on those ports.  Different scan types (TCP Connect, SYN, UDP, etc.) allow for stealthier scanning.
    *   **Service Version Detection:** Identify the version of software running on each open port. This information is crucial for identifying known vulnerabilities.
    *   **Operating System Detection:** Attempt to identify the operating system running on the target host.
    *   **Firewall Detection:**  Attempt to determine if a firewall is present and its rules.
    *   **Scripting Engine (NSE - Nmap Scripting Engine):**  Extend Nmap's functionality with custom scripts written in Lua.  NSE scripts can be used for vulnerability detection, exploitation, and network discovery.
*   **Advanced Techniques:**
    *   **Using NSE Scripts for Vulnerability Scanning:**  Leverage the Nmap Scripting Engine to automatically scan for specific vulnerabilities.
    *   **Evading Firewalls:**  Use various techniques (e.g., fragmentation, decoy scanning, source port manipulation) to bypass firewall restrictions.
    *   **Banner Grabbing:** Obtain detailed information about the services running on open ports by directly interacting with them.
    *   **Performing Stealth Scans:** Conduct scans that are less likely to be detected by intrusion detection systems (IDS).
*   **Resources:**
    *   [Nmap Official Website](https://nmap.org/)
    *   [Nmap Documentation](https://nmap.org/docs/)
    *   [Nmap Scripting Engine (NSE) Documentation](https://nmap.org/book/nse.html)

## Metasploit Framework

*   **Description:** A powerful and widely used penetration testing framework that provides a vast collection of exploits, payloads, and auxiliary modules. Metasploit allows you to test and validate vulnerabilities, automate exploitation, and perform post-exploitation activities.  It has a command-line interface (msfconsole) and a commercial web interface (Metasploit Pro).
*   **Key Uses:**
    *   **Exploitation of Known Vulnerabilities:**  Utilize a large database of exploits to target known vulnerabilities in systems and applications.
    *   **Payload Generation:**  Create malicious code (payloads) to execute on target systems after successful exploitation.  Payloads can be used to gain a shell, upload files, or perform other actions.
    *   **Post-Exploitation:**  Gather information about the compromised system, escalate privileges, maintain access, and pivot to other systems on the network.
    *   **Auxiliary Modules:**  Use auxiliary modules for scanning, fuzzing, and other tasks.
    *   **Social Engineering:**  Metasploit can be used to create and deliver social engineering attacks (e.g., phishing emails).
*   **Advanced Techniques:**
    *   **Writing Custom Exploits:**  Develop your own exploits for previously unknown vulnerabilities (zero-day exploits).
    *   **Developing Custom Payloads:**  Create payloads that are specifically designed for the target environment and bypass antivirus software.
    *   **Using Meterpreter:** Meterpreter is an advanced Metasploit payload that provides a rich set of features for post-exploitation, including file system access, process management, keylogging, and webcam access.
    *   **Bypassing Antivirus:**  Use techniques such as encoding, encryption, and obfuscation to bypass antivirus detection.
*   **Resources:**
    *   [Metasploit Documentation](https://docs.metasploit.com/)
    *   [Rapid7 Metasploit Tutorials](https://docs.rapid7.com/metasploit/)
    *   [Exploit Database](https://www.exploit-db.com/)

## Wireshark

*   **Description:** A powerful and versatile network protocol analyzer (also known as a packet sniffer). Wireshark captures and analyzes network traffic in real-time, allowing you to inspect communication patterns, identify potential security issues, and debug network protocols.
*   **Key Uses:**
    *   **Analyzing Network Traffic for Suspicious Activity:**  Identify unusual communication patterns, such as excessive traffic to a specific host, or the use of uncommon protocols.
    *   **Debugging Network Protocols:** Troubleshoot network connectivity issues and analyze protocol behavior.
    *   **Identifying Unencrypted Data:** Detect sensitive information (e.g., passwords, credit card numbers) being transmitted in cleartext.
    *   **Reconstructing Network Streams:** Reassemble TCP streams to view the complete communication between two hosts.
    *   **Analyzing Malware Traffic:**  Examine the network traffic generated by malware to understand its behavior and identify command-and-control (C&C) servers.
*   **Advanced Techniques:**
    *   **Using Filters:**  Apply filters to focus on specific types of traffic (e.g., traffic to a specific host, traffic using a specific protocol).  Wireshark uses a powerful filter syntax.
    *   **Following TCP Streams:**  Reassemble and analyze complete TCP conversations.
    *   **Analyzing VoIP Traffic:**  Capture and analyze VoIP (Voice over IP) traffic to identify security issues or eavesdrop on conversations (legally, with permission!).
    *   **Writing Custom Dissectors:**  Develop custom dissectors to analyze protocols that are not natively supported by Wireshark.
*   **Resources:**
    *   [Wireshark Official Website](https://www.wireshark.org/)
    *   [Wireshark Documentation](https://www.wireshark.org/docs/)
    *   [Wireshark Filters](https://www.wireshark.org/docs/man-pages/wireshark-filter.html)

## Additional Core Tools to Consider

*   **Hydra/Medusa:** Password cracking tools for brute-forcing authentication credentials against various services (SSH, FTP, HTTP, etc.).
*   **John the Ripper:** A password cracking tool primarily used for offline password cracking.  Can crack password hashes from various sources.
*   **tcpdump:** A command-line packet analyzer (similar to Wireshark) that is often used for capturing network traffic on servers or embedded devices.
*   **Netcat ("nc"):** A versatile command-line tool for reading and writing data across network connections. Can be used for port scanning, banner grabbing, file transfer, and creating reverse shells.
*   **OWASP ZAP (Zed Attack Proxy):** Another popular web application security scanner, similar to Burp Suite, but open-source.
*   **DirBuster/gobuster:** Tools used to discover hidden directories and files on web servers.
*   **Searchsploit**: A command-line tool for searching the Exploit Database.
