___

FTP = **File Transfer Protocol**
It is an application-layer protocol in the TCP/IP model.

FTP does not move files directly.
It uses:
- TCP for reliable communication
- Commands to control operations
- Separate connections for control and data

FTP is a **stateful client-server protocol**

The server remembers:
- who you are
- your authentication state
- current directory
- transfer mode
- permissions
- session information

## FTP architecture
There are two main components:

![[Pasted image 20260824023752.png]]

The client initiates communication.
The server listens.

### Ports used by FTP
FTP uses 2 channels.
1. Control Channel
2. Data Channel
#### 1. Control Channel
The **control channel** is used to **send commands and receive responses** between the FTP client and server.
- It is established on **TCP port 21**.
- It remains open for the duration of the FTP session.
- It **does not normally carry the actual file contents**.
- It carries commands such as:
    - `USER` — username
    - `PASS` — password
    - `LIST` — request directory listing
    - `RETR` — download a file
    - `STOR` — upload a file
    - `QUIT` — end the session

**Example:**  
You tell the FTP server: _“Download `report.pdf`.”_  
That request is sent through the **control channel**.

#### 2. Data Channel
The **data channel** is used to **transfer the actual data** between the client and server.

- It carries **files**, directory listings, etc.
- A separate connection is created for data transfer.
- In **active FTP**, the server typically uses **TCP port 20** for the data connection.
- In **passive FTP**, the server opens a dynamically chosen port for the data connection.

**Example:**  
After you request `report.pdf` through the control channel, the actual `report.pdf` file is transferred through the **data channel**.

# **Working of FTP**
## 1. Starting an FTP connection (packet level)
You type:
```
ftp 10.129.10.20
```
Your computer moves to the next step. If using a hostname, DNS resolution is done.
#### Step 1: DNS resolution
If using a hostname:
```
ftp.company.com
```

Your computer asks DNS:
```
What is ftp.company.com?
```

DNS replies:
```
10.129.10.20
```

#### Step 2: TCP three-way handshake
1. Your machine (client) to server: "Can we communicate?"
```
Client → Server

SYN
```

2. Server replies: Yes
```
Server → Client

SYN ACK
```

3. Connection established.
```
Client → Server

ACK
```

Now TCP session exists:
```
Client: random port 49152 <---------> Server: port 21

Real Example
192.168.1.5:49152 <---------> 10.129.10.20:21
```

## 2. FTP server greeting
Immediately after connection:
Server sends:
```
220 Welcome to FTP Server
```

This is called the: **Banner**

Example:
vsFTPd:
```
220 (vsFTPd 3.0.3)
```

Why attackers care:
Because it leaks:
- software name
- version
- sometimes OS

Example:
```
220 Microsoft FTP Service
```
Attacker learns:
"Windows IIS FTP."

## 3. FTP authentication process
FTP authentication uses commands.
```
Client: USER bob
↓
Server: 331 Please specify password
↓
Client: PASS password123
↓
Server: 230 Login successful
```
`331 and other numbers` are an **FTP response code** sent by the server.

Now authenticated.
The state changes:
Before:
```
Unauthenticated
```

After:
```
Authenticated user=bob
```

## 4. Anonymous FTP — detailed explanation ⭐⭐⭐
**Anonymous FTP** is a special type of FTP access that allows someone to connect to an FTP server **without having a normal personal account** on that server.

The idea is essentially:
> “I don't have a personal FTP account, but I'd like to access the files that the server has made publicly available.”

For example:
```text
Client → USER anonymous
Server → 331 Please specify password
```

The server is saying:
> “Anonymous access is allowed. Now provide the password field.”

The important thing is that the password is **not normally used to verify a real secret associated with a specific user**.
A common convention is to enter an email address:
```text
Client → PASS user@example.com
```

The server might then respond:
```text
Server → 230 Login successful
```

So:
```text
USER anonymous
PASS user@example.com
```
can result in an anonymous FTP session.

##### What happens after anonymous login?
Suppose a server contains:
```text
/public
   ├── software.zip
   ├── manual.pdf
   └── readme.txt

/private
   ├── employees.txt
   └── salaries.xlsx
```
The administrator might configure anonymous FTP so that anonymous users can access only `/public`.

Now the user is authenticated **as the anonymous FTP identity**.
But that doesn't necessarily mean they have unrestricted access.

The server may impose permissions such as:
```text
Anonymous user
   │
   ├── Read /public       ✓
   ├── Download files     ✓
   ├── Upload files       ✗
   ├── Delete files       ✗
   └── Access /private    ✗
```

This is a very important distinction:
> **Successful authentication does not mean unlimited permissions.**

Authentication answers:
> **“Who are you?”**

Authorization answers:
> **“What are you allowed to do?”**

##### Why would a server allow anonymous FTP?
There are legitimate reasons.
For example, an organization might want to distribute publicly available files:

```
Anonymous FTP Server
   │
   ├── drivers.zip
   ├── documentation.pdf
   ├── software.zip
   └── updates.zip
```
Anyone can download those files without creating an account.
Historically, anonymous FTP was widely used for **public software distribution and file archives**.

##### Why do attackers test for anonymous FTP?
This is where the security concern comes in.

If a server allows anonymous FTP, an attacker may check whether that access has been **configured securely**.

For example, they might discover:
```text
USER anonymous
     ↓
230 Login successful
```

That tells them:
> “This FTP server permits anonymous access.”

But **that alone does not mean the server is vulnerable**.
The next question is what anonymous users are actually allowed to do.

A properly configured server might allow:
```text
Download public files ✓
Upload files         ✗
Delete files         ✗
Access private files ✗
```
That's relatively controlled.

A badly configured server might accidentally allow anonymous users to upload files into sensitive or publicly accessible directories.
That can create serious security problems.

##### Why is anonymous FTP considered a security risk?
The biggest issue is *==misconfiguration==*.

Imagine an administrator intends:
```text
Anonymous → download public files only
```

but accidentally configures:
```text
Anonymous → read + write + delete
```
Now anyone who can connect anonymously might have much more access than intended.

## 5. FTP Commands
| Category | Command | Full Form / Meaning | Purpose | Real Command Example | Example Server Response |
|---|---|---|---|---|---|
| 🔐 Authentication | `USER` | User | Provides username | `USER bob` | `331 Please specify password` |
| 🔐 Authentication | `PASS` | Password | Provides password | `PASS myPassword123` | `230 Login successful` |
| 📁 Directory | `PWD` | Print Working Directory | Shows current directory | `PWD` | `257 "/home/bob"` |
| 📁 Directory | `CWD` | Change Working Directory | Changes directory | `CWD uploads` | `250 Directory changed` |
| 📁 Directory | `CDUP` | Change Directory Up | Moves to parent directory | `CDUP` | `200 Directory changed` |
| 📋 Listing | `LIST` | List | Displays files and directories | `LIST` | `150 Opening data connection` → `226 Transfer complete` |
| 📋 Listing | `NLST` | Name List | Lists names of files/directories | `NLST` | `150 Opening data connection` |
| 📥 File Transfer | `RETR` | Retrieve | Downloads a file | `RETR report.pdf` | `150 Opening data connection` → `226 Transfer complete` |
| 📤 File Transfer | `STOR` | Store | Uploads a file | `STOR assignment.pdf` | `150 Opening data connection` → `226 Transfer complete` |
| 📤 File Transfer | `APPE` | Append | Adds data to an existing file | `APPE server.log` | `150 Opening data connection` |
| 🗑️ File Management | `DELE` | Delete | Deletes a file | `DELE old.txt` | `250 Delete operation successful` |
| ✏️ File Management | `RNFR` | Rename From | Specifies the file to rename | `RNFR old.txt` | `350 Ready for RNTO` |
| ✏️ File Management | `RNTO` | Rename To | Specifies the new filename | `RNTO new.txt` | `250 Rename successful` |
| 📁 Directory Management | `MKD` | Make Directory | Creates a directory | `MKD backups` | `257 "/home/bob/backups" created` |
| 🗑️ Directory Management | `RMD` | Remove Directory | Removes a directory | `RMD oldfiles` | `250 Directory removed` |
| ⚙️ Transfer | `TYPE` | Transfer Type | Selects ASCII/binary transfer type | `TYPE I` | `200 Type set to I` |
| ⚙️ Transfer | `MODE` | Transfer Mode | Sets transfer mode | `MODE S` | `200 Mode set to S` |
| ⚙️ Transfer | `STRU` | File Structure | Sets file structure | `STRU F` | `200 Structure set to F` |
| 🔄 Session | `NOOP` | No Operation | Tests/keeps connection active | `NOOP` | `200 NOOP command successful` |
| 🚪 Session | `QUIT` | Quit | Ends the FTP session | `QUIT` | `221 Goodbye` |

## 6. Active FTP vs Passive FTP
The main difference is **who creates the data connection**.
- **Active FTP:** The **server connects back to the client**.
- **Passive FTP:** The **client connects to the server**.

This matters because firewalls and NAT devices often block incoming connections to the client

### Active FTP
In **Active FTP**, the **client tells the server where the client is listening**.
The client essentially says:
> "I'm listening on this IP address and port. When I need data, connect back to me."

###### Step 1: Control connection
The client first connects to the FTP server's control port:
```text
Client                         Server
  │                              │
  │──────── TCP connection ─────>│
  │                              │
  │                         Port 21
```
The client sends FTP commands through this connection.

###### Step 2: Client sends `PORT`
The client sends something like:
```text
PORT 192,168,1,5,195,80
```

This tells the server:
> "For the data connection, connect to IP `192.168.1.5` and port calculated from `195,80`."

The IP address is straightforward:
```text
192,168,1,5
```
means:
```text
192.168.1.5
```

###### Step 3: Calculate the port
FTP's `PORT` command represents the port using two numbers:
```text
p1,p2
```

The port is calculated as:
```text
Port = (p1 × 256) + p2
```

Here:
```text
p1 = 195
p2 = 80
```

Therefore:
```text
Port = (195 × 256) + 80
     = 49920 + 80
     = 50000
```

So:
```text
PORT 192,168,1,5,195,80
```

means:
```text
IP address = 192.168.1.5
Port       = 50000
```

###### Step 4: Server connects back
Now the important part.
The **server initiates the data connection**:
```text
                Control connection
Client ─────────────────────────────> Server
                                      Port 21


                Data connection
Client <───────────────────────────── Server
Port 50000                            Port 20
```

So the direction is:
```text
Server:20 ──────────> Client:50000
```
The server is connecting **back to the client**.
That's why it is called **Active FTP**.

##### Why is Active FTP a problem?
The problem is that the client is often behind a **firewall or NAT router**.

The client may have a private IP:
```text
192.168.1.5
```
That address is not directly reachable from the Internet.

The client says:
```text
PORT 192,168,1,5,195,80
```

But the FTP server is on the Internet.
It may try:
```text
Server ───────> 192.168.1.5:50000
```

The firewall/NAT may say:
> ❌ "This is an incoming connection. I didn't expect it, so I'll block it."

Therefore, the data connection can fail.

## 7. Passive FTP
**Passive FTP** was designed to solve much of this problem.

Instead of the server connecting back to the client:
> **The server opens a listening port, and the client connects to it.**

This is much more firewall-friendly because the client is already allowed to make outgoing connections.

---

###### Step 1: Control connection

Again, the client first connects to the server:
```text
Client ─────────────────> Server (Port 21)   
```

This is the control connection.

###### Step 2: Client sends `PASV`
The client sends:
```text
PASV
```

`PASV` means:
> **Passive**

The client is essentially asking:
> "Server, please open a port for the data connection and tell me where it is."

###### Step 3: Server chooses a port
The server might respond:
```text
227 Entering Passive Mode (10,129,10,20,195,80)
```

This tells the client:
```text
IP address = 10.129.10.20
p1 = 195
p2 = 80
```

The client calculates the port:
```text
Port = (195 × 256) + 80 = 50000
```

So the server is telling the client:
> "Connect to me at `10.129.10.20:50000` for the data connection."

###### Step 4: Client connects to server

Now look at the direction:
```text
Client ─────────────────> Server (Port 50000)
```

The **client initiates the data connection**.
So we have:
```text
Control connection:

Client ────────────────> Server:21


Data connection:

Client ────────────────> Server:50000
```

## 8. File transfer process
| Step | Client Action | Server Response / Action | Connection Used | What It Means |
|---|---|---|---|---|
| 1 | `get secret.txt` | FTP client prepares a download request | Client-side command | User wants to download `secret.txt` |
| 2 | `PASV` | Server enters Passive Mode | Control Connection | Client asks the server to prepare a data port |
| 3 | — | `227 Entering Passive Mode (...,195,80)` | Control Connection | Server tells the client which IP/port to use |
| 4 | — | Server listens on port `50000` | Data Connection | Server is ready to send the file |
| 5 | Client connects to `Server:50000` | Server accepts the connection | Data Connection | Data connection is established |
| 6 | `RETR secret.txt` | Server receives the request | Control Connection | Client asks server to retrieve `secret.txt` |
| 7 | — | `150 Opening BINARY mode data connection` | Control Connection | Server is about to start sending the file |
| 8 | — | Server sends file bytes | Data Connection | Actual contents of `secret.txt` are transferred |
| 9 | — | `226 Transfer complete` | Control Connection | Server confirms the file was transferred successfully |
| 10 | — | Data connection closes | Data Connection | File transfer is finished; control connection may remain open |

## 9. Security Vuln

| # | Vulnerability | Weakness / Cause | Example | What an Attacker May Do | Risk / Impact | Solution / Mitigation |
|---|---|---|---|---|---|---|
| 1 | 🔓 No Encryption | Normal FTP sends usernames, passwords, commands, and files as plaintext | `USER admin`<br>`PASS Secret123` | Sniff network traffic and read credentials/data | Credential theft, data exposure | Use **FTPS** or **SFTP** |
| 2 | 👤 Anonymous Access | Server allows login without a normal user account | `anonymous_enable=YES`<br><br>`ftp target`<br>`anonymous` | Access publicly available files and potentially discover sensitive files | Unauthorized file access, information disclosure | Disable anonymous access unless required; restrict permissions |
| 3 | 🔑 Weak Passwords | Users use easily guessable/common passwords | `admin:admin`<br>`ftp:ftp`<br>`password:password` | Attempt password guessing/brute-force attacks using tools such as **Hydra, Medusa, or Ncrack** | Account compromise | Use strong unique passwords, rate limiting, account lockout, and MFA where supported |
| 4 | ✍️ Writable Directories | FTP user has write access to a web-accessible directory | `/var/www/html`<br>`FTP writable` | Upload a malicious file such as `shell.php` | Possible remote code execution if the web server executes the uploaded file | Prevent FTP writes to web-executable directories; use least-privilege permissions |
| 5 | 📂 Directory Traversal | FTP server fails to properly validate file paths | `../../../../etc/passwd` | Escape the intended FTP directory and access files outside it | Unauthorized file access / information disclosure | Properly validate and normalize paths; restrict users to their designated directories |
| 6 | ⚠️ Old / Vulnerable Software | FTP server software is outdated and contains known vulnerabilities | `vsftpd 2.3.4` | Identify known vulnerabilities and potentially exploit the vulnerable software | Possible unauthorized access or remote code execution | Keep FTP software updated and remove unsupported versions |
| 7 | 🕵️ Information Disclosure | FTP banner reveals software name and version | `220 ProFTPD 1.3.5` | Identify the FTP software and version, then search for known vulnerabilities | Helps attackers fingerprint the server and select attacks | Hide/minimize version information and keep software patched |
