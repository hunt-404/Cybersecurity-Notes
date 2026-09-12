___

## Linux File System Basics
A **file system** is the method Linux uses to organize, store, and retrieve data from storage devices.

Think of it like a library:
```
Storage Disk
     |
     |
 File System
     |
 ----------------
 |      |        |
Books  Rooms   Index
Files  Dirs    Metadata
```
Linux uses a tree-like structure starting from `/` (root).

#### Why does it matter?
Every Linux task depends on the file system:
- Installing software
- Finding logs
- Managing users
- Securing servers
- Performing penetration tests

Example:
A web server error:
```
Website Down
     |
Check Logs
     |
/var/log/
     |
Find Error
```

#### How it works
Linux stores:
- Files → actual data
- Directories → file organization
- Metadata → permissions, owner, timestamps

Basic structure:
```
/
|
├── home
│    └── user
│         └── documents
|
├── etc
│    └── configuration files
|
└── var
     └── logs
```

##### Linux vs Windows File System
| Feature        | Linux           | Windows          |
| -------------- | --------------- | ---------------- |
| Root           | `/`             | `C:\`            |
| Drive letters  | No              | Yes              |
| Path separator | `/`             | `\`              |
| Case sensitive | Yes             | Usually No       |
| Hidden files   | Starts with `.` | Hidden attribute |
| Configuration  | Text files      | Registry + files |

### Root Directory `/`
`/` is the top of everything.

Example:
```
/
├── home
├── etc
├── var
├── usr
└── tmp
```
Everything exists under `/`.

#### Hidden Files
Linux hides files starting with:
```
.
```

Examples:
```
.bashrc
.ssh
.gitconfig
```

View:
```bash
ls -la
```

Example:
```
.
..
.bashrc
.ssh
Documents
```

### Linux File System Commands & Examples
| Command          | Purpose                                                          | Syntax / Usage          | Example Output                                                          |
| ---------------- | ---------------------------------------------------------------- | ----------------------- | ----------------------------------------------------------------------- |
| `pwd`            | Shows the current working directory (location)                   | `pwd`                   | `/home/user`                                                            |
| `ls`             | Lists files and directories                                      | `ls`                    | Shows files in the current directory                                    |
| `ls -la`         | Lists all files including hidden files with detailed information | `ls -la`                | `-rw-r--r-- user user notes.txt`<br>`drwxr-xr-x user user Documents`    |
| `cd`             | Changes the current directory                                    | `cd /etc`               | Moves user to `/etc` directory                                          |
| `cd ~`           | Returns to the user's home directory                             | `cd ~`                  | `/home/user`                                                            |
| `tree`           | Displays directory structure in a tree format                    | `tree /etc`             | Shows folders and subfolders visually                                   |
| `tree` (Install) | Installs tree command if not available                           | `sudo apt install tree` | Installs tree package                                                   |
| `file`           | Identifies the type of a file                                    | `file image.png`        | `PNG image data`                                                        |
| `stat`           | Shows detailed file metadata                                     | `stat file.txt`         | Displays:<br>• File size<br>• Owner<br>• Permissions<br>• Modified time |

___

# Level 2 - Linux Directory Structure (FHS)

## Filesystem Hierarchy Standard (FHS)
The **Linux File System Hierarchy Standard (FHS)** defines the recommended structure of directories inside `/`.
Think of it as Linux's organizational map.

**Why does it matter?**
As a Linux administrator or security professional, you constantly need to know:
- Where are configuration files?
- Where are logs?
- Where are user files?
- Where are programs installed?
- Where can attackers hide?

### Linux Directory Structure
| Directory | Purpose             | Example Files          | Security Importance          |
| --------- | ------------------- | ---------------------- | ---------------------------- |
| `/`       | Root of filesystem  | Everything starts here | Critical control point       |
| `/bin`    | Essential commands  | ls, cp, mv             | Malware may replace commands |
| `/boot`   | Boot files          | kernel, grub           | Boot attacks possible        |
| `/dev`    | Device files        | sda, tty               | Hardware access              |
| `/etc`    | Configuration files | passwd, ssh config     | High-value target            |
| `/home`   | User data           | documents, SSH keys    | User privacy                 |
| `/lib`    | System libraries    | libc.so                | Library hijacking            |
| `/opt`    | Optional software   | third-party apps       | Software isolation           |
| `/proc`   | Kernel information  | cpuinfo, processes     | Information leakage          |
| `/root`   | Root user's home    | admin files            | Privileged data              |
| `/tmp`    | Temporary files     | cache files            | Malware hiding place         |
| `/usr`    | User programs       | applications           | Large software area          |
| `/var`    | Changing data       | logs, databases        | Incident investigation       |

![[Pasted image 20260912055017.png|600]]

#### `/etc` — Configuration Center (Editable Text Configuration)
Contains system configuration.

Example:
```
/etc
|
├── passwd
├── shadow
├── ssh
├── hosts
└── nginx
```

##### Important files
| File                   | Purpose           |
| ---------------------- | ----------------- |
| `/etc/passwd`          | User accounts     |
| `/etc/shadow`          | Password hashes   |
| `/etc/hosts`           | Local DNS mapping |
| `/etc/fstab`           | Disk mounting     |
| `/etc/ssh/sshd_config` | SSH settings      |

#### `/home` — User Data
Example:
```
/home
 |
 ├── ali
 │    |
 │    ├── Documents
 │    ├── Downloads
 │    └── .ssh
 |
 └── sara
```

**Important:**

SSH keys:
```
/home/user/.ssh/
```

Contains:
```
id_rsa
id_rsa.pub
authorized_keys
```

**Security:**

Bad permissions:
```bash
chmod 777 ~/.ssh
```
Can expose accounts.

Correct:
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
```

#### `/var` — Variable Data
Stores data that changes frequently.

Structure:
```
/var
|
├── log
├── www
├── mail
└── lib
```
###### Important:

Logs:
```
/var/log/
```

Examples:
```
auth.log
syslog
nginx/
apache2/
```

View:
```bash
sudo tail -f /var/log/auth.log
```

###### Security Use:
Investigate:
- Failed SSH login
- Malware activity
- Unauthorized access



