
# LazySysAdmin Enumeration & Exploitation

## Network Discovery

```bash
sudo netdiscover -r 192.168.0.0/24
```

- Performed network discovery to identify active hosts within the local subnet.
- Identified the target machine as:
  - `PCS Systemtechnik GmbH`

```bash
ping 192.168.0.104
```

- Verified that the target machine was active and reachable on the network.

![Network Discovery](../Screenshots/netdiscoverandping-1.png)

---

## Initial Nmap Scan

```bash
nmap -sV -sC 192.168.0.104
```

- Performed an initial service enumeration scan against the target system.
- Identified multiple open ports and services including:
  - SSH
  - HTTP
  - SMB
  - MySQL
  - Additional network services

![Nmap Scan](../Screenshots/nmapscan-2.png)

---

## Web Directory Enumeration

```bash
dirb http://192.168.0.104 -r
```

- Performed directory brute forcing against the HTTP service using DIRB.
- Enumerated hidden web directories and application paths.

![DIRB Enumeration](../Screenshots/subdirectory-3.png)

---

## Username Discovery

- While inspecting discovered web directories, identified a directory named:
  - `togie`

- Determined that the discovered directory name could potentially represent a valid username.

![Discovered Username](../Screenshots/dirbfound-4.png)

---

## SSH Brute Force Using Hydra

```bash
hydra -l togie -P /usr/share/wordlists/rockyou.txt ssh://192.168.0.104
```

- Used Hydra to perform a password brute force attack against the SSH service.
- Successfully identified valid SSH credentials:
  - Username: `togie`
  - Password: `12345`

![Hydra SSH Brute Force](../Screenshots/hydraonssh-5.png)

---

## SSH Access

```bash
ssh togie@192.168.0.104
```

- Successfully authenticated to the SSH service using the credentials obtained from Hydra.

![SSH Login](../Screenshots/sshlogin-6.png)

---

## Privilege Escalation

```bash
sudo su root
```

- Performed privilege escalation from the `togie` user account to the root user.
- Successfully obtained administrative/root privileges on the target system.

![Privilege Escalation](../Screenshots/privilege_escalation-7.png)

---

## Sensitive File Enumeration

```bash
cd /var/www/html/wordpress
ls
```

- Enumerated the default web root directory commonly used by Apache web servers.
- Identified the `wp-config.php` file within the WordPress installation directory.

![wp-config.php Discovery](../Screenshots/found_config.phpfile-8.png)

---

## Credential Discovery

```bash
cat wp-config.php
```

- Inspected the `wp-config.php` configuration file.
- Retrieved sensitive credentials including usernames and passwords stored within the file.

![Credentials Found](../Screenshots/found_usrname&passwrd-9.png)

---

## phpMyAdmin Login

- During previous web directory enumeration, identified a phpMyAdmin login page.
- Used the credentials discovered inside `wp-config.php` to authenticate successfully.

![phpMyAdmin Login](../Screenshots/login_subdirectory_sqlpage-10.png)

---

## MySQL Access

- Successfully authenticated to the MySQL/phpMyAdmin interface using the discovered credentials.
- Verified access to the database management portal.

![MySQL Compromised](../Screenshots/mysql_page_compromised-11.png)
