---
categories:
- CTF
- Writeup
date: 2025-09-09
draft: false
description: 
slug: htb-cap-ctf
image: img/cap-logo.png
tags:
- linux
- technical
title: HTB - Cap Writeup
---

# HTB - Cap

<figure>
<img
src="img/cap-image-1.png"
alt="HTBCapLogo" />
<figcaption aria-hidden="true">HTBCapLogo</figcaption>
</figure>

**Target**: 10.129.22.131

## Enumeration

We began our enumeration with an Nmap scan to identify open ports and services. The scan revealed three open ports: 21 (FTP), 22 (SSH), and 80 (HTTP).

<figure>
<img
src="img/cap-nmap-scan.png"
alt="NmapScanResults" />
<figcaption aria-hidden="true">NmapScanResults</figcaption>
</figure>

We attempted to log in to the FTP service using anonymous credentials, but the attempt was unsuccessful.

<figure>
<img
src="img/cap-ftp-initial.png"
alt="FTPAnonymousLoginAttempt" />
<figcaption aria-hidden="true">FTPAnonymousLoginAttempt</figcaption>
</figure>

Visiting the web server revealed a security dashboard. We don't much also from `IP Config` and `Network Status` tabs

<figure>
<img
src="img/cap-webserver-1.png"
alt="WebDashboard" />
<figcaption aria-hidden="true">WebDashboard</figcaption>
</figure>

<figure>
<img
src="img/cap-webserver-2.png"
alt="WebDashboardPage2" />
<figcaption aria-hidden="true">WebDashboardPage2</figcaption>
</figure>

<figure>
<img
src="img/cap-webserver-3.png"
alt="WebDashboardPage3" />
<figcaption aria-hidden="true">WebDashboardPage3</figcaption>
</figure>

We ran `feroxbuster` in the background to enumerate directories and files while manually exploring the web application.

<figure>
<img
src="img/cap-feroxbuster.png"
alt="FeroxbusterScan" />
<figcaption aria-hidden="true">FeroxbusterScan</figcaption>
</figure>

While inspecting the navigation bar, we noticed a IDOR Vulnerabilities on `Security Snapshot` link with a URL ending in `/4`. By manipulating the URL (e.g., changing to `/0`), we discovered other downloadable PCAP files.

<figure>
<img
src="img/cap-pcap-1.png"
alt="PCAPFileDiscovery" />
<figcaption aria-hidden="true">PCAPFileDiscovery</figcaption>
</figure>

## Exploitation

### Nathan Credential Discovery

Analyzing the PCAP file we download from `/0` in Wireshark, we identified an FTP login attempt with plaintext credentials.

<figure>
<img
src="img/cap-pcap-2.png"
alt="PCAPFTPLogin" />
<figcaption aria-hidden="true">PCAPFTPLogin</figcaption>
</figure>

<figure>
<img
src="img/cap-pcap-3.png"
alt="PCAPFTPCredentials" />
<figcaption aria-hidden="true">PCAPFTPCredentials</figcaption>
</figure>

Following the TCP stream, we extracted the credentials:

- **Username**: nathan
- **Password**: Buck3tH4TF0RM3!

<figure>
<img
src="img/cap-pcap-4.png"
alt="PCAP Credential Extraction" />
<figcaption aria-hidden="true">PCAP Credential Extraction</figcaption>
</figure>

### Gaining Access

Using the credentials `nathan:Buck3tH4TF0RM3!`, we successfully logged into the FTP service and retrieved the `user.txt` flag.

<figure>
<img
src="img/cap-ftp-login.png"
alt="FTPLoginSuccess" />
<figcaption aria-hidden="true">FTPLoginSuccess</figcaption>
</figure>

The same credentials also granted access to the system via `SSH`.

<figure>
<img
src="img/cap-ssh.png"
alt="SSHLogin" />
<figcaption aria-hidden="true">SSHLogin</figcaption>
</figure>

## Privilege Escalation

During enumeration on the system, we discovered that `python3.8` had capabilities that could be abused for privilege escalation.

<figure>
<img
src="img/cap-privesc-1.png"
alt="privesc1" />
<figcaption aria-hidden="true">SSHLogin</figcaption>
</figure>


\### What is Capabilities in Linux ?

Linux capabilities break down the full privileges of root into smaller, fine-grained permissions (e.g., `CAP_NET_BIND_SERVICE`, `CAP_SETUID`, `CAP_SYS_ADMIN`). Instead of giving a binary full root rights, the system can assign only the capability it needs.

However, if dangerous capabilities are set on interpreters (like Python), they can be abused for privilege escalation. For example, if Python has `CAP_SETUID`, an attacker can change their UID to 0 and spawn a root shell.

You can list file capabilities with:

```bash
getcap -r / 2>/dev/null
```

<figure>
<img
src="img/cap-privesc-2.png"
alt="PrivilegeEscalationStep2" />
<figcaption aria-hidden="true">PrivilegeEscalationStep2</figcaption>
</figure>

We executed the following command to gain a root shell:

``` bash
python3.8 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

<figure>
<img
src="img/cap-privesc-3.png"
alt="RootShell" />
<figcaption aria-hidden="true">RootShell</figcaption>
</figure>

This command leveraged the `setuid` capability to escalate privileges, granting us root access and completing the challenge.

## Loot

### Credentials

- **Username**: nathan
- **Password**: Buck3tH4TF0RM3!

### Flags

- **User Flag**: Retrieved via FTP.
- **Root Flag**: Obtained after privilege escalation.
