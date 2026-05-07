# FTP Enumeration & Exploitation

## Initial Nmap Scan

```bash
nmap -sV -sC -p21 192.168.0.110
```

- Performed an initial service enumeration scan against the FTP service running on port 21.
- Identified the FTP service version.
- Detected anonymous login access enabled.
- Retrieved FTP banner information.
- Identified a vulnerable FTP software version.

- ![Nmap Scan](../Screenshots/ftpnmap-1.png)

- ___
- 
