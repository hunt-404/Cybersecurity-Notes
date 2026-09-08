___
### Entry Level Commands
| **Purpose**                    | **Nmap Command**               | **Functional Breakdown**                                                                          |
| ------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------- |
| **Host Discovery (Ping Scan)** | `nmap -sn 192.168.1.0/24`      | Finds live hosts on a subnet without scanning ports.                                              |
| **TCP SYN Scan (Stealth)**     | `nmap -sS target.com`          | Fast, default scan that doesn't complete the TCP connection (requires root/sudo).                 |
| **TCP Connect Scan**           | `nmap -sT target.com`          | Completes the full TCP handshake; slower but doesn't require root privileges.                     |
| **Scan Specific Ports**        | `nmap -p 22,80,443 target.com` | Scans only the exact ports specified.                                                             |
| **Scan All Ports**             | `nmap -p- target.com`          | Scans all 65,535 TCP ports instead of just the top 1,000.                                         |
| **Version Detection**          | `nmap -sV target.com`          | Probes open ports to determine the exact service and application version running.                 |
| **OS Detection**               | `nmap -O target.com`           | Analyzes packet responses to guess the target's operating system.                                 |
| **Default Script Scan**        | `nmap -sC target.com`          | Runs a collection of default, safe Nmap scripts (NSE) for basic vulnerability checks.             |
| **Aggressive Scan**            | `nmap -A target.com`           | Combines OS detection (`-O`), version detection (`-sV`), script scanning (`-sC`), and traceroute. |
| **Timing Template (Speed)**    | `nmap -T4 target.com`          | Speeds up the scan (scale of 0-5). `T4` is the standard for fast, reliable scanning.              |
| **Output to All Formats**      | `nmap -oA results target.com`  | Saves scan results in three formats (.nmap, .xml, .gnmap) for documentation or other tools.       |
### Flags
| **Flag**                        | **Meaning**                                         | **Example**                                                    |
| ------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| **Target Specification**        |                                                     |                                                                |
| `iL <FILE>`                     | Input targets from a file/list.                     | `nmap -iL targets.txt`                                         |
| `-p <PORTS>`                    | Specify ports to scan.                              | `nmap -p 80,443 10.10.10.50`                                   |
| `-F`                            | Fast mode (scans fewer ports).                      | `nmap -F 10.10.10.50`                                          |
| `--top-ports <NUM>`             | Scan the `<NUM>` most common ports.                 | `nmap --top-ports 100 10.10.10.50`                             |
| **Host Discovery**              |                                                     |                                                                |
| `-sL`                           | List Scan (lists targets without sending packets).  | `nmap -sL 10.10.10.0/24`                                       |
| `-sn`                           | Ping Scan (host discovery only).                    | `nmap -sn 10.10.10.0/24`                                       |
| `-Pn`                           | Skip host discovery (treat all targets as online).  | `nmap -Pn 10.10.10.50`                                         |
| `-PS/PA/PU/PY <PORTS>`          | TCP SYN/ACK, UDP, or SCTP discovery probes.         | `nmap -PS80,443 10.10.10.50`                                   |
| **Scan Techniques**             |                                                     |                                                                |
| `-sS`                           | TCP SYN scan (stealthy, half-open).                 | `nmap -sS 10.10.10.50`                                         |
| `-sT`                           | TCP Connect scan.                                   | `nmap -sT 10.10.10.50`                                         |
| `-sU`                           | UDP port scan.                                      | `nmap -sU 10.10.10.50`                                         |
| `-sA`                           | TCP ACK scan (map firewall rule sets).              | `nmap -sA 10.10.10.50`                                         |
| `-sN / -sF / -sX`               | TCP Null, FIN, and Xmas scans.                      | `nmap -sF 10.10.10.50`                                         |
| **Service & Version Detection** |                                                     |                                                                |
| `-sV`                           | Probe open ports to determine service/version info. | `nmap -sV 10.10.10.50`                                         |
| `--version-intensity <0-9>`     | Set version scan intensity.                         | `nmap -sV --version-intensity 5 10.10.10.50`                   |
| `--version-light`               | Limit to most likely probes.                        | `nmap -sV --version-light 10.10.10.50`                         |
| **Script Scanning**             |                                                     |                                                                |
| `-sC`                           | Run default Nmap Scripting Engine (NSE) scripts.    | `nmap -sC 10.10.10.50`                                         |
| `--script <SCRIPTS>`            | Run specific scripts or categories.                 | `nmap --script vuln 10.10.10.50`                               |
| `--script-args <ARGS>`          | Pass arguments to NSE scripts.                      | `nmap --script smb-brute --script-args user=admin 10.10.10.50` |
| **OS Detection**                |                                                     |                                                                |
| `-O`                            | Enable OS detection via TCP/IP fingerprinting.      | `nmap -O 10.10.10.50`                                          |
| `--osscan-guess`                | Guess OS results more aggressively.                 | `nmap -O --osscan-guess 10.10.10.50`                           |
| **Timing & Performance**        |                                                     |                                                                |
| `-T0` to `-T5`                  | Set timing template.                                | `nmap -T4 10.10.10.50`                                         |
| `--host-timeout <TIME>`         | Give up on target after this long.                  | `nmap --host-timeout 30s 10.10.10.50`                          |
| `--max-retries <NUM>`           | Cap the number of probe retransmissions.            | `nmap --max-retries 2 10.10.10.50`                             |
| **Firewall Evasion & Spoofing** |                                                     |                                                                |
| `-D <DECOYS>`                   | Cloak a scan with decoys.                           | `nmap -D RND:5 10.10.10.50`                                    |
| `-S <IP_ADDRESS>`               | Spoof source address.                               | `nmap -S 10.10.10.99 10.10.10.50`                              |
| `--spoof-mac <MAC>`             | Spoof MAC address.                                  | `nmap --spoof-mac Apple 10.10.10.50`                           |
| `--badsum`                      | Send packets with bogus checksum.                   | `nmap --badsum 10.10.10.50`                                    |
| **Output & Verbosity**          |                                                     |                                                                |
| `-v`                            | Increase output verbosity.                          | `nmap -v 10.10.10.50`                                          |
| `-vv`                           | Increase verbosity further.                         | `nmap -vv 10.10.10.50`                                         |
| `--reason`                      | Display the reason a port is in a specific state.   | `nmap --reason 10.10.10.50`                                    |
| `-oN <FILE>`                    | Output scan in normal format.                       | `nmap -oN output.txt 10.10.10.50`                              |
| `-oX <FILE>`                    | Output scan in XML format.                          | `nmap -oX output.xml 10.10.10.50`                              |
| `-oG <FILE>`                    | Output scan in Grepable format.                     | `nmap -oG output.gnmap 10.10.10.50`                            |
| `-oA <BASENAME>`                | Output in normal, XML, and grepable formats.        | `nmap -oA result 10.10.10.50`                                  |
| **Miscellaneous**               |                                                     |                                                                |
| `-6`                            | Enable IPv6 scanning.                               | `nmap -6 fe80::1`                                              |
| `-A`                            | Enable OS, version, script, and traceroute.         | `nmap -A 10.10.10.50`                                          |
| `--resume <FILE>`               | Resume an aborted scan.                             | `nmap --resume scan.log`                                       |

> [!h4] ### What are NSE Scripts?
> **NSE** stands for **Nmap Scripting Engine**.
> Generally, NSE scripts are **Lua scripts that extend Nmap's functionality**. Nmap normally specializes in discovering hosts, ports, services, and versions. NSE lets you perform additional, more specialized tasks against those services.
> 
> A simple way to think about it:
> > **Nmap scans → NSE scripts investigate.**
> 
> ##### Without an NSE script
> ```bash
> nmap -sV 10.10.10.50
> ```
> 
> Nmap might tell you:
> ```
> PORT    STATE SERVICE VERSION
> 22/tcp  open  ssh     OpenSSH 9.2
> 80/tcp  open  http    Apache 2.4.57
> ```
> You know **what services are running**.
> ##### With an NSE script
> ```bash
> nmap --script <SCRIPT> 10.10.10.50
> ```
> 
> The script can perform additional tasks, such as:
> - Gathering additional service information
> - Detecting operating-system or application details
> - Enumerating users or resources
> - Checking configurations
> - Detecting known vulnerabilities
> - Testing authentication mechanisms
> - Performing protocol-specific queries
> - Checking SSL/TLS configurations
> - Collecting information from web servers, databases, DNS, SSH, SMB, etc.
> 
> #### Common NSE categories
> 
> Nmap provides many scripts organized into categories:
> 
> |Category|General purpose|
> |---|---|
> |`auth`|Authentication-related checks|
> |`broadcast`|Discover information through broadcast requests|
> |`brute`|Perform brute-force authentication attempts|
> |`default`|Scripts commonly considered useful for normal scans|
> |`discovery`|Discover additional information about hosts/services|
> |`dos`|Denial-of-service testing|
> |`exploit`|Exploitation-related scripts|
> |`external`|Uses external services|
> |`fuzzer`|Fuzz protocols/services|
> |`intrusive`|Potentially disruptive or noticeable checks|
> |`malware`|Detect possible malware/backdoors|
> |`safe`|Generally designed to avoid disruptive behavior|
> |`vuln`|Vulnerability detection|
> 
> You can run a specific script:
> ```bash
> nmap --script <SCRIPT> <TARGET>
> ```
> 
> Or a category:
> ```bash
> nmap --script discovery <TARGET>
> ```
> 
> Or multiple scripts:
> ```bash
> nmap --script <SCRIPT1>,<SCRIPT2> <TARGET>
> ```
> 
> For authorized testing, it's also worth checking a script before running it:
> ```bash
> nmap --script-help <SCRIPT>
> ```
> This tells you what the script does and its associated categories.
> 

___

### SMB - Nmap Commands

| Level        | Purpose                          | Nmap Command                                                                                                                                                     | What It Does                                                                                      |
| ------------ | -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| Beginner     | SMB port/service discovery       | **Syntax:**<br>`nmap -p 139,445 <TARGET>`<br><br>**Example:**<br>`nmap -p 139,445 10.10.10.50`                                                                   | Checks common SMB ports (139 and 445) to determine whether SMB is exposed.                        |
| Beginner     | Version detection                | **Syntax:**<br>`nmap -sV -p 139,445 <TARGET>`<br><br>**Example:**<br>`nmap -sV -p 139,445 10.10.10.50`                                                           | Identifies the SMB service implementation and, where possible, its version.                       |
| Beginner     | SMB NSE enumeration              | **Syntax:**<br>`nmap -p 445 --script smb-enum* <TARGET>`<br><br>**Example:**<br>`nmap -p 445 --script smb-enum* 10.10.10.50`                                     | Runs SMB enumeration scripts that can discover shares, users, domains, and other SMB information. |
| Intermediate | SMB OS discovery                 | **Syntax:**<br>`nmap -p 445 --script smb-os-discovery <TARGET>`<br><br>**Example:**<br>`nmap -p 445 --script smb-os-discovery 10.10.10.50`                       | Attempts to identify the remote OS, hostname, domain/workgroup, and other SMB metadata.           |
| Intermediate | SMB protocol/dialect enumeration | **Syntax:**<br>`nmap -p 445 --script smb-protocols <TARGET>`<br><br>**Example:**<br>`nmap -p 445 --script smb-protocols 10.10.10.50`                             | Identifies supported SMB dialects and can reveal whether legacy SMBv1 is enabled.                 |
| Intermediate | Share enumeration                | **Syntax:**<br>`nmap -p 445 --script smb-enum-shares <TARGET>`<br><br>**Example:**<br>`nmap -p 445 --script smb-enum-shares 10.10.10.50`                         | Attempts to enumerate available SMB shares and information about their accessibility.             |
| Intermediate | User/domain enumeration          | **Syntax:**<br>`nmap -p 445 --script smb-enum-users <TARGET>`<br><br>**Example:**<br>`nmap -p 445 --script smb-enum-users 10.10.10.50`                           | Attempts to enumerate SMB users and domain/account information when permitted.                    |
| Intermediate | SMB security checks              | **Syntax:**<br>`nmap -p 445 --script smb-security-mode <TARGET>`<br><br>**Example:**<br>`nmap -p 445 --script smb-security-mode 10.10.10.50`                     | Checks SMB security configuration, including authentication and signing characteristics.          |
| Intermediate | SMB vulnerability assessment     | **Syntax:**<br>`nmap -p 445 --script smb-vuln* <TARGET>`<br><br>**Example:**<br>`nmap -p 445 --script smb-vuln* 10.10.10.50`                                     | Runs available SMB-specific vulnerability detection scripts.                                      |
| Advanced     | MS17-010 assessment              | **Syntax:**<br>`nmap -p 445 --script smb-vuln-ms17-010 <TARGET>`<br><br>**Example:**<br>`nmap -p 445 --script smb-vuln-ms17-010 10.10.10.50`                     | Checks for indicators of the MS17-010/EternalBlue vulnerability.                                  |
| Advanced     | SMB2 capabilities and time       | **Syntax:**<br>`nmap -p 445 --script smb2-capabilities,smb2-time <TARGET>`<br><br>**Example:**<br>`nmap -p 445 --script smb2-capabilities,smb2-time 10.10.10.50` | Queries SMB2 capabilities and server time, providing additional protocol and host information.    |
| Advanced     | Comprehensive SMB NSE assessment | **Syntax:**<br>`nmap -sV -p 139,445 --script "smb-*" <TARGET>`<br><br>**Example:**<br>`nmap -sV -p 139,445 --script "smb-*" 10.10.10.50`                         | Runs the SMB NSE script family for broader enumeration and security assessment.                   |
| Advanced     | General vulnerability NSE scan   | **Syntax:**<br>`nmap -p 445 --script vuln <TARGET>`<br><br>**Example:**<br>`nmap -p 445 --script vuln 10.10.10.50`                                               | Runs vulnerability-category NSE scripts; results may include checks beyond SMB.                   |

