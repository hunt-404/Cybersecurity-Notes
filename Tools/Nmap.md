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
