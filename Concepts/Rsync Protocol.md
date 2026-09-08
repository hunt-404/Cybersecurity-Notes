___
# LEVEL 1 — Basics

> [!h4] #### What Is rsync?
> **rsync** (Remote Sync) is a technology used to **copy and synchronize files between locations efficiently**.
>
> It can synchronize
> - Files between two computers
> - Files between local directories
> - Backups between servers
> - Website files between machines
> - Data between a workstation and a remote server
>
> The key idea:
> > rsync transfers only the parts of files that have changed instead of copying everything again.


> [!h4] #### Why Does rsync Exist?
> Before rsync, administrators often used simple file copying methods:
>
> Example:
> ```
> Server A                    Server B
> 
> website.zip  ----------->   website.zip
> (5 GB)                      (5 GB)
> ```
>
> **Problem**:
> If only one small file changed inside the 5 GB archive, the entire 5 GB had to be transferred again.
>
> This wastes:
> - Network bandwidth
> - Time
> - Storage resources
>
> > rsync solves this by being **intelligent about synchronization**.


> [!h4] ### Common rsync Usage Models
> There are two major ways rsync works.
>
> > [!h4] #### Model 1: Local Synchronization
> > Both directories exist on the same machine.
> >
> > Example:
> > ```
> > Computer
> > 
> > /home/user/data
> > 
> >         |
> >         |
> >         ▼
> > 
> > /backup/data
> > ```
> >
> > Command idea:
> > ```bash
> > rsync source destination
> > ```
> >
> > Example:
> > ```bash
> > rsync photos/ backup/
> > ```
> >
> > Meaning:
> > "Synchronize the photos directory into backup."
>
> > [!h4] #### Model 2: Remote Synchronization
> > The source and destination are on different machines.
> >
> > Example:
> > ```
> > Attacker/Test Machine             Server
> > 
> >      Client                       Server
> >         |                           |
> >         |------ rsync protocol ---->|
> >         |                           |
> > ```
> >
> > Used for:
> > - Backups
> > - Server replication
> > - File deployment

> [!h4] ### Security Perspective
>
> ###### Why Attackers Care About rsync
> From a penetration testing perspective, rsync is interesting because it may expose:
> - Sensitive files
> - Backup data
> - Configuration files
> - Credentials
> - Source code
> - Internal documents
>
> A poorly configured rsync service can accidentally become a data leak.
>
> ##### **Common Security Weaknesses**
> | #   | Security Weakness         | EXPLANATION                                                                            | Risk                                                 |
> | --- | ------------------------- | -------------------------------------------------------------------------------------- | ---------------------------------------------------- |
> | 1   | Anonymous Access          | No authentication is required to access the rsync server.                              | Attackers may download exposed data.                 |
> | 2   | Weak Access Control       | A user intended for `/backup/public` can access `/backup/all_system_files`.            | Sensitive files may be unintentionally exposed.      |
> | 3   | Sensitive Backup Exposure | Backups may contain database dumps, password files, SSH keys, and application secrets. | Attackers can obtain highly valuable information.    |
> | 4   | Poor File Permissions     | Example: `-rwxrwxrwx backup.tar.gz`                                                    | Too many users may access or modify sensitive files. |

> [!h4] #### Normal Working
> Before discussing attacks, understand how rsync works normally.
> ###### Main Components
> A basic rsync setup contains:
> ```
> +-------------+          +-------------+
> |             |          |             |
> |   Client    |  ----->  |   Server    |
> |             |          |             |
> +-------------+          +-------------+
> 
>      Source              Destination
> ```
>
> |Role|Description|Example|
> |---|---|---|
> |Client|Machine that starts the synchronization.|💻 Your laptop|
> |Server|Machine that provides or receives files.|🖥️ A backup server|


___

# LEVEL 2 — Deep Architecture & Internal Mechanics
## Internal Architecture
rsync has two major operating modes.
```
                 rsync
                   |
        ------------------------
        |                      |
        v                      v

   Local Mode              Remote Mode

                           |
              --------------------------
              |                        |
              v                        v

        SSH Transport          rsync Daemon
```

> [!h] ### Local rsync Mode
> Everything happens on the same machine.
> 
> Example:
> ```
> Laptop
> 
> /home/user/data
> 
>         |
>         |
>         v
> 
> /backup/data
> ```
> 
> No network is involved.
> #### Internal Components
> ```
> +----------------+
> | rsync command  |
> +----------------+
>         |
>         v
> +----------------+
> | File Scanner   |
> +----------------+
>         |
>         v
> +----------------+
> | Comparison     |
> | Engine         |
> +----------------+
>         |
>         v
> +----------------+
> | Transfer       |
> | Engine         |
> +----------------+
>         |
>         v
> +----------------+
> | Destination    |
> +----------------+
> ```
> 

> [!h4] ### Remote rsync Over SSH
> This is the most common configuration.
> 
> Example:
> ```
> Client Machine
> 
> rsync
> 
>    |
>    |
>  SSH connection
>    |
>    |
>    v
> 
> Remote Server
> 
> rsync process
> ```
> 
> 
> #### What Happens Internally?
> 
> Suppose you run:
> ```
> rsync files.txt user@server:/backup/
> ```
> 
> **The flow is:**
> ```
> 1. Local rsync starts
> 
>         |
>         v
> 
> 2. SSH connection created
> 
>         |
>         v
> 
> 3. Authentication happens
> 
>         |
>         v
> 
> 4. Remote rsync process starts
> 
>         |
>         v
> 
> 5. Both rsync processes communicate
> 
>         |
>         v
> 
> 6. Files are compared
> 
>         |
>         v
> 
> 7. Required data is transferred
> ```
> 

> [!h4] ### rsync Daemon Mode
> **Why is it called a "Daemon"?**
> A **daemon** is simply a program that runs in the background and waits for requests.
> The rsync daemon normally listens on:
> ```text
> TCP port 873
> ```
> 
> This is a special rsync server.
> Instead of using SSH, it performs like this:
> ```
> Client
>    |
>    | TCP connection
>    | Port 873
>    ↓
> +------------------+
> |   rsync daemon   |
> |   TCP 873        |
> +------------------+
> ```
> 
> #### Why Does rsync Have a Daemon?
> Because sometimes organizations want:
> - Faster transfers
> - Dedicated file synchronization service
> - Anonymous access (if configured)
> - Centralized backup systems
> 
> #### rsync Daemon Architecture
> 
> ```
>                  Network
> 
>                     |
>                     |
>                     v
> 
>           +----------------+
>           | Port 873       |
>           | rsync daemon   |
>           +----------------+
> 
>                     |
>                     |
> 
>           +----------------+
>           | Module Manager |
>           +----------------+
> 
>                     |
>                     |
> 
>           +----------------+
>           | Shared Folder  |
>           +----------------+
> ```

> [!h4] ### Step by Step Internal Flow
> #### Step 1: File Discovery
> - **Action:** `rsync` recursively scans the source directory to build a complete inventory.
> - **Metadata Collected:** File names, relative paths, permissions, ownership, size, and modification timestamps.
> - 
> #### Step 2: File Comparison
> - **Action:** Compares source and destination metadata to determine which files require synchronization.
>     
> |**Condition**|**Verdict**|**Action**|
> |---|---|---|
> |**Size & Timestamp match**|Unchanged|No transfer|
> |**Size or Timestamp differs**|Modified|Flagged for sync|
> #### Step 3: Delta Transfer Algorithm
> This is rsync's most important feature.
> - **Action:** Splits large, modified files into fixed-size blocks to avoid re-transferring entire files.
> - **Mechanism:** Uses checksums (mathematical fingerprints) to compare individual blocks.
>     
> ```
> Source Blocks:      [ A ] [ B ] [ C ] [ D ]
> Destination Blocks: [ A ] [ B ] [ X ] [ D ]
> Comparison:           ✓     ✓     ✗     ✓
> Action: Transmit block C only.
> ```
> 
> #### Step 4: Data Transfer
> - **Action:** Executes the transfer based on comparison findings.
> - **Scope:** Sends only newly added files, missing files, or modified blocks. Identical data streams are completely bypassed.
>     
> #### Step 5: Metadata Synchronization
> - **Action:** Replicates file attributes from source to destination after data payloads are successfully transferred.
> 
>    **Attributes Synced:**
>     - File permissions (e.g., `chmod 644`)
>     - Ownership (UID/GID)
>     - Modification timestamps (`mtime`)
>     - Symbolic and hard links
>
>#### Visual Flow Diagram     
![[Pasted image 20260905025234.png]]

___

# LEVEL 3 - rsync Use
#### General Command Format
```bash
rsync [OPTIONS] SOURCE DESTINATION
```

#### Core Flags and Tags
| **Flag / Tag**    | **Long Form**          | **Crisp Explanation**                                                      |
| ----------------- | ---------------------- | -------------------------------------------------------------------------- |
| **`-a`**          | `--archive`            | Preserves recursiveness, permissions, ownership, timestamps, and symlinks. |
| **`-v`**          | `--verbose`            | Shows detailed file names and transfer status during execution.            |
| **`-h`**          | `--human-readable`     | Formats numbers and file sizes in KB, MB, or GB.                           |
| **`-z`**          | `--compress`           | Compresses data blocks during network transit to save bandwidth.           |
| **`-P`**          | `--partial --progress` | Displays live progress and retains incomplete files to allow resume.       |
| **`-n`**          | `--dry-run`            | Simulates the execution without reading/writing actual changes.            |
| **`--delete`**    | `--delete`             | Removes files from destination that no longer exist on the source.         |
| **`-e`**          | `--rsh`                | Specifies the remote shell utility (e.g., custom SSH commands).            |
| **`--exclude`**   | `--exclude=PATTERN`    | Skips files or directories matching the specified pattern.                 |
| **`--bwlimit`**   | `--bwlimit=RATE`       | Caps I/O and network transfer speed to a maximum KB/s.                     |
| **`--list-only`** | `--list-only`          | Displays source files and directories without copying any data.            |

#### Commands to Interact with a Remote Host
| **Step**                            | **Action**                    | **Command**                                             | **Example**                                       |
| ----------------------------------- | ----------------------------- | ------------------------------------------------------- | ------------------------------------------------- |
| **1. Enumerate Shares**             | List available modules        | `rsync -av --list-only <TARGET_IP>::`                   | `rsync -av --list-only 10.10.10.50::`             |
| **2. List Files**                   | Inspect share contents        | `rsync -av --list-only <TARGET_IP>::<MODULE>/`          | `rsync -av --list-only 10.10.10.50::backups/`     |
| **3. Download (Pull)**              | Download share files          | `rsync -avzP <TARGET_IP>::<MODULE>/ ./local_dir/`       | `rsync -avzP 10.10.10.50::backups/ ./loot/`       |
| **4. Upload (Push)**                | Test write access             | `rsync -avzP ./test.txt <TARGET_IP>::<MODULE>/`         | `rsync -avzP ./test.txt 10.10.10.50::backups/`    |
| **5. Custom Port (If needed)**      | Target non-default port       | `rsync -av --port=<PORT> --list-only <TARGET_IP>::`     | `rsync -av --port=8730 --list-only 10.10.10.50::` |
| **6. Authentication (If required)** | Authenticate with credentials | `rsync -avz <USER>@<TARGET_IP>::<MODULE>/ ./local_dir/` | `rsync -avz admin@10.10.10.50::backups/ ./loot/`  |

#### Step by Step Real Basic HTB Machine
| **Step**                | **Purpose**                            | **Command**                                         | **Result / What to Look For**                       |
| ----------------------- | -------------------------------------- | --------------------------------------------------- | --------------------------------------------------- |
| **1. Enumerate Shares** | Discover accessible daemon modules     | `rsync -av --list-only 10.129.228.37::`             | Found share name: `public`                          |
| **2. Inspect Module**   | List files inside the discovered share | `rsync -av --list-only 10.129.228.37::public`       | Found target file: `flag.txt`                       |
| **3. Download File**    | Transfer file without                  | `rsync -av 10.129.228.37::public/flag.txt flag.txt` | File downloaded to current directory (`/home/kali`) |
| **4. Verify File**      | Confirm file exists locally            | `ls`                                                | `flag.txt` is visible in folder contents            |
| **5. Read Content**     | View the retrieved flag                | `cat flag.txt`                                      | Output: `72eaf5344ebb84908ae543a719830519`          |
#### Remote Operations (Over SSH)
| **Task**                  | **Command**                                            | **Explanation**                                                 |
| ------------------------- | ------------------------------------------------------ | --------------------------------------------------------------- |
| **Push to Remote Host**   | `rsync -avzP /local/dir/ user@remote:/dst/`            | Compresses and sends local files to a remote system.            |
| **Pull from Remote Host** | `rsync -avzP user@remote:/src/ /local/dir/`            | Downloads files from a remote server to the local path.         |
| **Custom SSH Port**       | `rsync -avzP -e "ssh -p 2222" /src/ user@remote:/dst/` | Directs the encrypted transfer through a non-standard SSH port. |
| **Remote Exact Mirror**   | `rsync -avz --delete /src/ user@remote:/dst/`          | Ensures remote destination perfectly matches the local source.  |

#### Advanced & Optimization Operations
| **Task**                     | **Command**                                         | **Crisp Explanation**                                      |
| ---------------------------- | --------------------------------------------------- | ---------------------------------------------------------- |
| **Exclude Single Pattern**   | `rsync -av --exclude='*.log' /src/ /dst/`           | Syncs the folder but ignores all matching pattern files.   |
| **Exclude Multiple Folders** | `rsync -av --exclude={'.git','cache'} /src/ /dst/`  | Skips specific subdirectories during sync.                 |
| **Exclude via File List**    | `rsync -av --exclude-from='ignore.txt' /src/ /dst/` | Reads ignore patterns from an external text file.          |
| **Bandwidth Throttling**     | `rsync -avP --bwlimit=2000 /src/ /dst/`             | Limits data throughput to 2000 KB/s (~2 MB/s).             |
| **Resume Large File**        | `rsync -avP --append /src/file.iso /dst/file.iso`   | Resumes an interrupted transfer by appending missing data. |

## rsync Daemon Commands
The `rsync` daemon runs as a persistent service listening on TCP port 873, exposing configured shares called **modules** directly without requiring SSH.

#### General Daemon Syntax
Accessing an `rsync` daemon uses a double colon (`::`) or the `rsync://` URL scheme:

```bash
rsync [OPTIONS] SOURCE rsync://[USER@]HOST[:PORT]/MODULE[/PATH]
# OR
rsync [OPTIONS] SOURCE [USER@]HOST::MODULE[/PATH]
```

#### Daemon-Specific Flags
| **Flag / Tag**        | **Long Form**          | **Crisp Explanation**                                                    |
| --------------------- | ---------------------- | ------------------------------------------------------------------------ |
| **`--daemon`**        | `--daemon`             | Starts the `rsync` process as a background service/daemon.               |
| **`--config`**        | `--config=FILE`        | Overrides the default configuration file location (`/etc/rsyncd.conf`).  |
| **`--port`**          | `--port=PORT`          | Specifies a custom TCP port instead of default 873.                      |
| **`--password-file`** | `--password-file=FILE` | Reads the authentication password from a file (avoids terminal prompt).  |
| **`--list-only`**     | `--list-only`          | Lists available modules or directory contents without transferring data. |

#### Client Commands (Interacting with a Daemon)
|**Task**|**Command**|**Crisp Explanation**|**Example**|
|---|---|---|---|
|**List Public Modules**|`rsync rsync://remote_host/`|Queries the server to list all publicly visible shared modules.|`rsync rsync://192.168.1.50/`|
|**List Module Contents**|`rsync -av --list-only remote_host::backup_mod`|Displays the files inside a module without downloading them.|`rsync -av --list-only 192.168.1.50::backups`|
|**Pull from Module**|`rsync -avzP remote_host::backup_mod /local/path/`|Downloads all contents of the remote module to a local path.|`rsync -avzP 192.168.1.50::backups /home/user/data/`|
|**Push to Module**|`rsync -avzP /local/path/ remote_host::backup_mod`|Uploads local files to the specified remote daemon module.|`rsync -avzP /home/user/data/ 192.168.1.50::backups`|
|**Authenticated Sync**|`rsync -avz user@remote_host::backup_mod /local/path/`|Authenticates using modular credentials defined in rsyncd.secrets.|`rsync -avz operator@192.168.1.50::confidential /local/backup/`|
|**Scripted Password**|`rsync -av --password-file=pass.txt user@host::mod /dest`|Uses an automated password file (chmod 600) for headless jobs.|`rsync -av --password-file=/etc/rsync.pass backupuser@192.168.1.50::db_dumps /var/backups/`|
|**Custom Port Connection**|`rsync -av --port=8730 remote_host::backup_mod /dst/`|Connects to a daemon running on a non-standard port.|`rsync -av --port=8730 192.168.1.50::backups /opt/restore/`|

#### Server Administration Commands
| **Task**                  | **Command**                                      | **Crisp Explanation**                                                |
| ------------------------- | ------------------------------------------------ | -------------------------------------------------------------------- |
| **Start Daemon Manually** | `rsync --daemon`                                 | Launches the standalone daemon using `/etc/rsyncd.conf`.             |
| **Start Custom Config**   | `rsync --daemon --config=/opt/rsync/custom.conf` | Launches the daemon bound to an alternate configuration path.        |
| **Systemd Start**         | `sudo systemctl start rsync`                     | Starts the managed system service on modern Linux distributions.     |
| **Systemd Enable**        | `sudo systemctl enable rsync`                    | Configures the `rsync` daemon service to auto-start on boot.         |
| **Check Daemon Status**   | `sudo systemctl status rsync`                    | Verifies whether the service is running and reports runtime errors.  |
| **Verify Open Port**      | `ss -tulpn \| grep :873`                         | Verifies that the host system is actively listening on TCP port 873. |
