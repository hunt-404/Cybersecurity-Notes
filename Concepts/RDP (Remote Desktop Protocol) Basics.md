___

| Term | Meaning |
|---|---|
| Remote access | Accessing a computer/device from another location |
| Remote desktop | Remotely interacting with another computer's graphical desktop |
| Client | The device/application initiating the connection |
| Host | The computer being remotely accessed |
| Authentication | Proving that the user is authorized |
| RAT | Malware that provides unauthorized remote access |
### What is RDP?
**Remote Desktop Protocol (RDP)** is a Microsoft-developed protocol for remotely interacting with a Windows graphical desktop.

An RDP connection lets a client remotely:
- See the server's desktop.
- Send keyboard and mouse input.
- Run applications.
- Redirect selected local resources such as drives, printers, clipboard, audio, or smart cards.
- Establish additional application/protocol functionality through RDP channels.

| Protocol | Default port |
|---|---:|
| TCP | 3389 |
| UDP | 3389 |
Don't assume 3389 is always used. Administrators can change the RDP listener port, and during an assessment you should treat RDP as a **service**, not merely "TCP/3389."

### RDP vs RDS
| Term    | Full Name               | What It Is                                | Purpose                                                                                               |
| ------- | ----------------------- | ----------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| **RDP** | Remote Desktop Protocol | A network protocol                        | Defines how a remote client communicates with a Windows system to provide a remote desktop experience |
| **RDS** | Remote Desktop Services | A Windows server-side technology/platform | Provides remote desktop and remote application functionality                                          |
RDP = protocol
RDS = Windows service/platform implementing remote desktop functionality

### Basic RDP Client/Server Architecture

>**Client = the computer you are sitting at.**  
  **Server/host = the computer whose desktop you are accessing.**

### Remote Access Protocols & Technologies — Timeline
| Era           | Protocol / Technology                        | Access Type    | Key Characteristics                              | Why Obsolete / Less Preferred                                       |
| ------------- | -------------------------------------------- | -------------- | ------------------------------------------------ | ------------------------------------------------------------------- |
| 1960s–70s     | **rlogin <br>(TCP 513)**                     | CLI            | Remote Unix login; host-based trust              | ❌ Weak trust + no encryption → SSH                                  |
| 1970s–80s     | **Telnet <br>(TCP 23)**                      | CLI            | Simple remote terminal                           | ❌ Plaintext credentials/data → SSH                                  |
| 1980s         | **rsh <br>(TCP 514)**                        | CLI            | Execute commands remotely on Unix                | ❌ No encryption + weak trust → SSH                                  |
| 1980s–90s     | **FTP <br>(TCP 21)**                         | File transfer  | Remote file upload/download                      | ❌ Plaintext authentication/data → SFTP/FTPS/HTTPS                   |
| 1990s         | **X11 <br>(TCP 6000+)**                      | GUI/apps       | Remote Unix graphical applications               | ❌ Direct exposure is insecure → SSH X11 forwarding/modern solutions |
| 1998–present  | **RDP <br>(TCP 3389*)**                      | GUI desktop    | Windows remote desktop, device/clipboard support | ✅ Not obsolete; still widely used                                   |
| 1998–present  | **VNC/RFB <br>(TCP 5900+)**                  | GUI desktop    | Cross-platform remote screen/control             | ✅ Not obsolete; security depends on implementation                  |
| 1990s–present | **Citrix ICA/HDX (Varies)**                  | GUI/apps       | Enterprise app/desktop delivery                  | ✅ Evolved rather than became obsolete                               |
| 1995–present  | **SSH <br>(TCP 22*)**                        | CLI/tunneling  | Encrypted remote login, tunneling, file transfer | ✅ Not obsolete; successor to Telnet/rlogin/rsh                      |
| 2000s–present | **WinRM<br>(TCP 5985/5986)**                 | Management/CLI | Windows remote management & automation           | ✅ Not obsolete                                                      |
| 2000s–present | **PowerShell Remoting <br>(TCP 5985/5986*)** | CLI/automation | Remote PowerShell administration                 | ✅ Not obsolete                                                      |
| 2010s–present | **Web-based remote access <br>(TCP 443)**    | GUI            | Remote access through browser/HTTPS              | ✅ Current approach; convenient through web infrastructure           |

> **Note:** `*` Ports may vary depending on configuration or implementation.

### What is `xfreerdp`?
`xfreerdp` is a **command-line RDP client**. It lets you connect from Linux, including Kali Linux, to a computer running **Remote Desktop Protocol (RDP)**.

Think of it as the Linux equivalent of Microsoft's **Remote Desktop client**.
#### What does it actually do?
Suppose you have a Windows machine at:
```text
192.168.1.50
```

with RDP enabled.
You can use:
```bash
xfreerdp /v:192.168.1.50
```

`xfreerdp` then:
1. Connects to the RDP service.
2. Performs the RDP negotiation.
3. Handles authentication.
4. Establishes a remote desktop session.
5. Displays the Windows desktop on your Kali machine.
6. Sends your keyboard/mouse input to the Windows machine.

##### `xfreerdp` Switches/Options 
| Option  | Meaning             | Example          |
| ------- | ------------------- | ---------------- |
| `/v`    | RDP server / target | `/v:10.10.10.20` |
| `/port` | Server port         | `/port:3390`     |
| `/u`    | Username            | `/u:admin`       |
| `/p`    | Password            | `/p:Password123` |
| `/d`    | Domain              | `/d:CORP`        |
| `/f`    | Fullscreen          | `/f`             |
| `/w`    | Screen width        | `/w:1366`        |
| `/h`    | Screen height       | `/h:768`         |
| size`   | Screen dimensions   | `/size:1366x768` |
We can use more than one switch in a command.
For Example:
```bash
xfreerdp /v:192.168.1.50 /cert:ignore /u:Administrator
```

