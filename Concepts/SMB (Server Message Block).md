___
##### What is SMB?
**SMB** stands for **Server Message Block**. At its core, SMB is a **Client-Server communication protocol**.

> **SMB** is the official language and set of rules the client must use to ask the server for access.

When you map a network drive in Windows (e.g., your `Z:` drive pointing to a company server), you are using SMB. When you send a document to a network printer, you are using SMB. It is the lifeblood of file and resource sharing in a Windows environment (though Linux uses it too, via a software suite called **Samba**).

![[Pasted image 20260827080950.png|500]]

#### The Transport Layer: Where Does SMB Live?
Computers communicate using **ports** and **protocols**:
- **Port:** A virtual endpoint used to identify different types of network traffic.
- **Protocol:** Rules that define how data is communicated between systems.
###### SMB Transport Methods
| Method            | Ports                 | Used with                                           | Description                                                |
| ----------------- | --------------------- | --------------------------------------------------- | ---------------------------------------------------------- |
| SMB over NetBIOS  | TCP 139, UDP 137, 138 | Older Windows networks                              | SMB runs over NetBIOS for naming and network communication |
| Direct-Hosted SMB | TCP 445               | Modern Windows systems (Starting from WIndows 2000) | SMB runs directly over TCP/IP without NetBIOS              |
**`NetBIOS`**
**NetBIOS** is essentially a name-resolution service. It allowed computers on a local network to find each other by name (e.g., `JOHN-PC`) rather than IP address.

```
SMB
├── Legacy → NetBIOS
│   ├── UDP 137 → Name Service
│   ├── UDP 138 → Datagram Service
│   └── TCP 139 → Session Service
│
└── Modern → Direct SMB
    └── TCP 445 → SMB over TCP/IP
```

#### The Concept of "Shares"
When a server makes a folder, printer, or resource available via SMB, it is called a **Share**.
Suppose a server has:
```text
Server
├── Public
├── Finance
├── Engineering
└── Printers
```

It might expose some of these as network shares:
```text
\\Server\Public
\\Server\Finance
\\Server\Engineering
```

A client might request:
> “I want to connect to `\\Server\Finance`.”

The server checks whether the authenticated user is allowed to access that share.

- Standard shares: Folders created and shared by an administrator, e.g. `\Finance_Docs or \HR_Policies`.
- Administrative Shares: Hidden Windows shares created for system administration.
- $ suffix: Administrative shares end with $, making them hidden from normal network browsing.
- Examples: `C$, ADMIN$, and IPC$`.

| Share | Simple meaning | Why it matters |
|---|---|---|
| `C$` | Provides network access to the C: drive | Admin access can expose the entire drive |
| `ADMIN$` | Points to the Windows directory, usually `C:\Windows` | Commonly used for remote administration |
| `IPC$` | Provides communication channels via Named Pipes | Important for SMB communication and potential Null Session attacks |
#### Authentication: The Bouncer at the Door
You cannot just walk up to an SMB server and grab files. You must authenticate (prove who you are). SMB relies on underlying Windows security mechanisms to handle this. The two main protocols used are:

| Authentication | How it works | Key points |
|---|---|---|
| NTLM | Uses a challenge-response process. The server sends a challenge, and the client generates a response using the user's password-derived hash. | Older Windows authentication protocol; vulnerable to attacks such as NTLM relay and commonly targeted by attackers. |
| Kerberos | The user authenticates with the Domain Controller, which issues a cryptographic ticket. The ticket is then presented to the SMB server to request access. | Modern authentication protocol used in Active Directory; provides ticket-based authentication and avoids sending the password directly to the SMB server. |
#### The Dialects: Versions of SMB
As time passed, SMB evolved. These versions are called **Dialects**. When a client and server first meet, they negotiate to find the highest dialect they both understand.

| Version | Introduced | Key features | Security |
|---|---|---|---|
| SMBv1 | 1984 | Very old SMB protocol with a large, complex command set | Extremely insecure; famously exploited by EternalBlue in the WannaCry attack |
| SMBv2 | 2006 (Windows Vista) | Simplified SMB with the command set reduced from 100+ commands to 19 | Faster, less complex, and more secure than SMBv1 |
| SMBv3 | 2012 (Windows 8) | Added features including end-to-end encryption | Protects SMB traffic from being read if intercepted |
#### SMB Command Format
`SMB_COM_NEGOTIATE` : just **a name for an SMB command**.

```
SMB_COM_NEGOTIATE
│   │   │
│   │   └── specific command: NEGOTIATE
│   └────── COM = command
└────────── SMB = Server Message Block
```
###### What does `COM` mean?
`COM` is short for **command**.
So you'll often see names following a pattern like:
```text
SMB_COM_XXXXX
```

You can read that as:
> **SMB command: XXXXX**

For example:

| Syntax                     | Simple meaning                        |
| -------------------------- | ------------------------------------- |
| SMB_COM_NEGOTIATE          | Negotiate the SMB protocol version    |
| SMB_COM_SESSION_SETUP_ANDX | Establish/authenticate an SMB session |
| SMB_COM_TREE_CONNECT_ANDX  | Connect to a shared resource          |
| SMB_COM_TREE_DISCONNECT    | Disconnect from a share               |
| SMB_COM_LOGOFF_ANDX        | End/log off the SMB session           |
###### One important thing: SMB1 vs SMB2/3
This is **very important** when you're learning cybersecurity.
Names beginning with things like:
```text
SMB_COM_...
```
are generally associated with **SMB1 terminology**.

SMB2/SMB3 use a different command naming scheme, such as:
```text
SMB2 NEGOTIATE
SMB2 SESSION_SETUP
SMB2 TREE_CONNECT
SMB2 READ
SMB2 WRITE
```

So you might see:
```text
SMB_COM_NEGOTIATE
```

and elsewhere:
```text
SMB2 NEGOTIATE
```
They are related concepts, but **they belong to different generations of the SMB protocol**.

## Level 2
### The 4-Step: The SMB Communication Lifecycle
Before a single file is transferred or a single exploit payload is detonated, the client and server must perform a strict handshake. Keep in mind that before this happens, your computer has already completed the standard **TCP 3-way handshake** (SYN, SYN-ACK, ACK) to establish a raw network connection on Port 445.
Once that TCP tunnel is open, the SMB protocol takes over in four distinct steps:

#### Step 1: Negotiation `(SMB_COM_NEGOTIATE)`
The server looks at the list, picks the highest, most secure dialect it also understands, and replies, "Let's speak SMBv2."

> **Client:** “These are the SMB versions I understand.”  
> **Server:** “I understand several of those. Let's use this one.”

**Why hackers care:**
- Exploits such as MS17-010/EternalBlue may test whether a server accepts **SMBv1**.
- **If accepted:** It can indicate **SMBv1 is enabled** and the target may be vulnerable.

#### Step 2: Session Setup `(SMB_COM_SESSION_SETUP_ANDX)`
Now the computers have agreed on the SMB language.

Next question:
> **“Who are you?”**

The client needs to authenticate.
- The client sends their authentication credentials (usually an NTLM hash or a Kerberos ticket.
- If the server accepts the credentials, it assigns the client a **UID (User ID)**. From this point on, the client includes this UID in every packet to say, "I am already authenticated; here is my badge number."

##### What does `_ANDX` mean?
You'll encounter names such as:
```text
SESSION_SETUP_ANDX
TREE_CONNECT_ANDX
```
**ANDX** was an SMB1 mechanism for chaining commands.

Imagine ordering food:
###### Without command chaining
```text
Client → "Authenticate"
Server → "Done"

Client → "Connect to share"
Server → "Done"

Client → "Do something else"
Server → "Done"
```
That's lots of back-and-forth.
###### With ANDX
```text
Client → "Authenticate AND then connect to this share"
Server → response
```
So **ANDX essentially means commands can be chained together to reduce communication round trips.**

The name comes from the idea of:
> **“Do this, AND eXecute the next command.”**
###### One important detail
`ANDX` is primarily associated with **SMB1**. SMB2/SMB3 use a different message structure and don't use the old ANDX command-chaining mechanism in the same way.

#### Step 3: Tree Connect `(SMB_COM_TREE_CONNECT_ANDX)`
- Authentication gets you through the “front door.”
- Now you need access to a specific room (a Share).
- Tree Connect requests access to a specific SMB share.
- The client sends a request with the share path, e.g. `\\192.168.1.50\IPC$`.
- The server then determines whether access to that share is allowed.

`\\192.168.1.50\IPC$` - This looks complicated, so let's break it apart.

| Part           | Meaning                                                      |
| -------------- | ------------------------------------------------------------ |
| `\\`           | Indicates a network path in Windows                          |
| `192.168.1.50` | IP address of the server                                     |
| `\`            | Separates parts of the path                                  |
| `IPC$`         | A special Windows administrative/IPC share                   |
| Share          | A network-accessible resource exposed by a computer          |
| Tree Connect   | SMB operation that connects your session to a specific share |
| TID            | Tree ID; identifies the connected share in SMB1              |

#### Step 4: Resource Access
You have:
- **UID** → identifies your authenticated session: **“Who am I?”**
- **TID** → identifies the share you're connected to: **“Where am I accessing?”**
Now the client can request operations.
###### Steps flow
| Step               | Client is basically saying        | Server gives back       |
| ------------------ | --------------------------------- | ----------------------- |
| 1. Negotiate       | “What SMB version can we use?”    | Selected SMB dialect    |
| 2. Session Setup   | “Here are my credentials.”        | UID/session information |
| 3. Tree Connect    | “I want this share.”              | TID/share information   |
| 4. File Operations | “I want to access this resource.” | Requested data/result   |

___

# Level 3
### **The Attack Surface & Enumeration**
Here are the four primary misconfigurations you are hunting for.

| Misconfiguration | What it means | Why it matters |
|---|---|---|
| Null Session | Server allows access without a username or password | Can expose information without authentication |
| Open Shares | Shares such as `\Backups` or `\HR` allow Guest/Anonymous read or write access | May expose sensitive files or allow unauthorized changes |
| Outdated Protocols (SMBv1) | Server still supports legacy SMBv1 dialects | SMBv1 contains known vulnerabilities, including those exploited by EternalBlue |
| Disabled SMB Signing | SMB packets are not cryptographically signed | Can enable attackers to perform NTLM relay attacks |
###### Null Session
A Null Session specifically targets the `IPC$` (Inter-Process Communication) share. It exploits the Session Setup phase we covered in Level 2.

**The Mechanical Flow of a Null Session:**
```
[Your Kali Linux]                                          [Target Windows Server]
       |                                                             |
       |--- 1. TCP 3-Way Handshake (Port 445) ---------------------->|
       |--- 2. Negotiate Protocol (SMBv2) -------------------------->|
       |--- 3. Session Setup (User: "", Password: "") -------------->|
       |<-- 4. Server Accepts (Assigns Anonymous/Guest UID) ---------|
       |--- 5. Tree Connect (Path: \\Target\IPC$) ------------------>|
       |<-- 6. Server Grants Access to Named Pipes (TID) ------------|
       |--- 7. RPC Commands via Named Pipes (List users, groups) --->|
       |<-- 8. Server returns sensitive Domain information ----------|
```

- **Why this exists:** Historically, Windows domains needed computers to be able to request lists of network resources before a user fully logged in.
- **The danger:** An attacker leverages this anonymous connection to pull the entire list of users on the domain, password policies, and security groups.

#### Tools to Exploit SMB
| Tool          | How it can be used for SMB                                                                            | Example command                                                                      |
| ------------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Nmap          | Identify SMB services, OS details, SMB versions, and available shares using NSE scripts               | `nmap -p 139,445 -sV --script smb-os-discovery.nse,smb-enum-shares.nse 192.168.1.10` |
| smbclient     | Enumerate shares and connect to accessible shares; useful for testing anonymous/Null Session access   | `smbclient -L \\\\192.168.1.10\\ -N`                                                 |
| enum4linux    | Perform broad SMB enumeration, including users, shares, OS details, and password policies             | `enum4linux -a 192.168.1.10`                                                         |
| NetExec (nxc) | Test SMB authentication across multiple hosts and quickly enumerate accessible shares and permissions | `nxc smb 192.168.1.0/24 -u '' -p '' --shares`                                        |

#### How to Find Vuln using `smbclient`
| **Step**                    | **Goal**                                  | **Command**                                                              | **What It Does / Key Flags**                                                     |
| --------------------------- | ----------------------------------------- | ------------------------------------------------------------------------ | -------------------------------------------------------------------------------- |
| **1. Identify Port**        | Confirm SMB is reachable                  | `nmap -p 139,445 -sV <TARGET_IP>`                                        | Scans ports 139/445 and grabs version banners.                                   |
| **2. List Shares (Null)**   | Check for anonymous access                | `smbclient -L //<TARGET_IP>/ -N`                                         | `-L` lists shares; `-N` sends no password.                                       |
| **3. List Shares (Guest)**  | Fallback if Null login is rejected        | `smbclient -L //<TARGET_IP>/ -U 'Guest'`                                 | Attempts share listing using the built-in `Guest` account.                       |
| **4. List Shares (Auth)**   | List shares with known credentials        | `smbclient -L //<TARGET_IP>/ -U 'user%pass'`                             | Passes valid credentials inline.                                                 |
| **5. Connect to Share**     | Access a specific share (e.g., `Backups`) | `smbclient //<TARGET_IP>/Backups -N`                                     | Opens interactive prompt `smb: \>` on the target share.                          |
| **6. Inspect Content**      | View files inside the share               | `smb: \> ls`                                                             | Lists files and directories in current remote path.                              |
| **7. Download Single File** | Pull a sensitive file to local box        | `smb: \> get config.txt`                                                 | Downloads `config.txt` to your current local Linux directory.                    |
| **8. Test Write Access**    | Check if share allows file uploads        | `smb: \> put test.txt`                                                   | Uploads local `test.txt` to test write/upload permissions.                       |
| **9. Bulk Exfiltration**    | Download all files recursively            | `smb: \> recurse ON`<br><br>`smb: \> prompt OFF`<br><br>`smb: \> mget *` | Toggles recursive mode, disables prompt confirmations, and downloads every file. |
| **10. Disconnect**          | Terminate the SMB session                 | `smb: \> exit`                                                           | Safely closes the connection and returns to local shell.                         |


