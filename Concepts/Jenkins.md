___
# Level 1: Foundations
### Jenkins
**Jenkins is an automation server used to automatically build, test, and deploy software.**

From a security perspective:
- Jenkins is a **CI/CD (Continuous Integration / Continuous Deployment) platform**.
- It connects developers' code changes with automated processes.
- Because Jenkins often has access to:
    - Source code
    - Build systems
    - Deployment servers
    - Secrets/API keys
    - Production environments
It becomes an attractive target during penetration tests.

#### Why Does Jenkins Exist?
**Before Jenkins:**
```
Developer writes code
        |
        ↓
Manual testing
        |
        ↓
Manual deployment
        |
        ↓
Human mistakes
```
Problems:
- Slow releases
- Inconsistent testing
- Deployment errors

**Jenkins introduced:**
```
Developer pushes code
          |
          ↓
       Jenkins
          |
   ----------------
   |      |       |
 Build  Test   Deploy
```
Automation makes software delivery faster and more reliable.

#### Important Terms
| #     | Terminology        | Full Form              | What It Is / Meaning                                          | Responsibilities / Examples                                                                | Key Idea                             |
| ----- | ------------------ | ---------------------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | ------------------------------------ |
| **1** | **CI**             | Continuous Integration | Automatically checking and testing new code changes.          | Developer commits code → `git push` → Jenkins detects change → Runs tests → Reports result | **Automatically test new code**      |
| **1** | **CD**             | Continuous Deployment  | Automatically releasing tested code.                          | Code → Build → Test → Deploy to Server                                                     | **Automatically deploy tested code** |
| **2** | **Jenkins Server** | —                      | The main Jenkins application/server.                          | Manages users, runs jobs, stores configurations, schedules tasks                           | **Controls and manages Jenkins**     |
| **3** | **Job**            | —                      | A task that Jenkins performs.                                 | Compile software, run security scans, deploy applications, execute scripts                 | **A specific task**                  |
| **4** | **Pipeline**       | —                      | A sequence of automated steps that forms a complete workflow. | Code Upload → Build → Security Scan → Testing → Deployment                                 | **Complete automated workflow**      |
| **5** | **Plugin**         | —                      | An extension that adds functionality to Jenkins.              | Git integration, Docker support, AWS/cloud deployment, security scanning                   | **Adds capabilities to Jenkins**     |

### Security Perspective
Why does Jenkins matter to penetration testers?
Because Jenkins frequently contains:
#### 1. Sensitive Information
Examples:
- Passwords
- API keys
- Cloud credentials
- SSH keys

Stored in:
- Job configurations
- Build files
- Environment variables
- Credentials store

#### 2. Command Execution Capability
Jenkins executes:
- Shell commands
- Scripts
- Build tools

Example:
```
Jenkins Job

Execute:

python build.py
```

If an attacker controls a job:
```
Attacker
    |
    ↓
 Jenkins Job
    |
    ↓
 Command Execution
```

#### 3. High Privilege Access
Jenkins often connects to:
- Production servers
- Internal networks
- Cloud platforms
A compromised Jenkins server may become a pivot point.

### Pentest Mindset
When you discover Jenkins:
Do not immediately attack.

Think:
```
"I found Jenkins"

        ↓

What version?

        ↓

Is authentication enabled?

        ↓

What information is exposed?

        ↓

Are plugins vulnerable?

        ↓

Can jobs execute commands?

        ↓

Are secrets exposed?
```

### Common Jenkins Components & Security Interests
| Component         | Purpose                | Security Interest         |
| ----------------- | ---------------------- | ------------------------- |
| Web Interface     | Admin/user interaction | Authentication weaknesses |
| Jobs              | Automation tasks       | Command execution         |
| Plugins           | Extend features        | Vulnerable code           |
| Credentials Store | Save secrets           | Credential exposure       |
| Build Workspace   | Temporary files        | Sensitive data leakage    |
| Pipeline Scripts  | Automation logic       | Injection issues          |

### Common Mistakes
| #     | Common Mistake                                    | Reality                                                                                                                                                                      |
| ----- | ------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1** | **"Jenkins is just a website."**                  | Jenkins is often connected to the **entire software delivery chain**, including source code, builds, testing, and deployment.                                                |
| **2** | **"Only the Jenkins version matters."**           | Jenkins security depends on **configuration, authentication, plugins, permissions, and exposed secrets** — not just the version.                                             |
| **3** | **"Finding Jenkins means instant exploitation."** | **Enumeration comes first.** You need to understand the Jenkins instance, its configuration, users, jobs, plugins, and permissions before determining possible attack paths. |

___

# Level 2: Internal Architecture

## Core Architecture
A simplified Jenkins architecture:
```
                  User
                   |
                   |
              Web Browser
                   |
                   |
             Jenkins Controller
                   |
       ┌───────────┼───────────┐
       |           |           |
   Jobs        Plugins     Credentials
       |
       |
   Build Queue
       |
       |
   Executors
       |
       |
 Jenkins Agents
       |
       |
 Target Systems
```

### 1. Jenkins Controller (Master) — Detailed Explanation
The **Jenkins Controller** is the central component of a Jenkins installation. It is responsible for coordinating the CI/CD system: deciding **what should run, when it should run, how it should run, and which agent should execute it**.

> **Important terminology:** Older Jenkins documentation commonly used **“Master”**. Modern Jenkins terminology uses **“Controller.”**

###### Purpose
The controller manages:
- User authentication
- Job scheduling
- Configuration
- Plugin management
- Build coordination

You can think of Jenkins as a company:
```

                        Jenkins
                           |
                           v
                +----------------------+
                | Jenkins Controller   |
                |                      |
                | - Authentication     |
                | - Scheduling         |
                | - Configuration      |
                | - Plugins            |
                | - Credentials        |
                +----------+-----------+
                           |
              +------------+------------+
              |            |            |
              v            v            v
         +---------+  +---------+  +---------+
         | Agent 1 |  | Agent 2 |  | Agent 3 |
         +---------+  +---------+  +---------+
              |            |            |
              v            v            v
           Build        Test          Deploy
```

> At a technical level, Jenkins is primarily a **Java-based application**.

#### Jenkins Home Directory
On many Linux installations, Jenkins uses:
```text
/var/lib/jenkins/
```
as its **JENKINS_HOME**.

However, this is not universal.
The actual Jenkins home directory depends on how Jenkins was installed and configured.
You can think of it as Jenkins' persistent storage area:
```text
JENKINS_HOME
      |
      +--- jobs/
      |
      +--- plugins/
      |
      +--- secrets/
      |
      +--- users/
      |
      +--- config.xml
      |
      +--- workspace/
      |
      +--- builds/
      |
      +--- logs/
      |
      +--- other Jenkins data
```
#### Security Relevance
The controller is highly valuable because it may contain:
- Credentials
- Build scripts
- Configuration files
- Plugin information
- User data

### 2. Jenkins Agents
Machines that actually perform the work.
###### Why Agents Exist
Some tasks require:
- More CPU
- Different operating systems
- Special software
###### Technical View
Agents communicate with the controller using:
- SSH
- JNLP/WebSocket
- TCP connections
##### Security Relevance
Agents are important because:
- They execute commands
- They may have network access
- They may contain sensitive files

A compromised agent can become:
```
Agent
 |
 ↓
Internal Network
 |
 ↓
Other Systems
```

### Common Architecture Mistakes
| Mistake                    | Impact                      |
| -------------------------- | --------------------------- |
| Running Jenkins as root    | Full system compromise risk |
| Exposing Jenkins publicly  | External attack surface     |
| Too many plugins           | Increased vulnerabilities   |
| Weak agent isolation       | Internal compromise         |
| Poor credential management | Secret exposure             |
| No access control          | Unauthorized changes        |

___

# Level 3: Enumeration & Vulnerability Assessment (Pentester Perspective)
### Enumeration Mindset
Follow:
```
Discover
   ↓
Identify Jenkins
   ↓
Fingerprint Version
   ↓
Analyze Configuration
   ↓
Check Authentication
   ↓
Identify Exposed Functionality
   ↓
Look for Attack Paths
```

|Step|Enumeration Phase|What to Check|Why It Matters (Security Reasoning)|Possible Findings|Next Action|
|---|---|---|---|---|---|
|**1**|**Service Discovery**|Identify exposed Jenkins service (ports, web pages, URLs)|First understand what is running before attacking|Jenkins on `8080`, `80`, `443`, `/jenkins/` path|Confirm Jenkins installation|
|**2**|**Service Identification**|Verify the application is actually Jenkins|Avoid false assumptions from ports alone|Jenkins logo, Dashboard, Build Now, Manage Jenkins|Start fingerprinting|
|**3**|**Version Fingerprinting**|Find Jenkins core version and plugin versions|Versions may reveal known weaknesses|Jenkins version, outdated plugins, Java version|Research possible security issues|
|**4**|**Authentication Check**|Determine login requirements and access level|Authentication controls who can access Jenkins|Anonymous access, login page, weak permissions|Analyze user privileges|
|**5**|**Permission Enumeration**|Check what users can view or execute|Weak permissions can expose sensitive functions|View jobs, trigger builds, admin features|Identify privilege opportunities|
|**6**|**Job Enumeration**|Review available Jenkins jobs/projects|Jobs often contain automation scripts and sensitive data|Build tasks, deployment jobs, source repositories|Inspect job details|
|**7**|**Build History Analysis**|Examine previous builds and console output|Developers may accidentally expose secrets|Passwords, API keys, tokens, internal paths|Extract useful information|
|**8**|**Configuration Review**|Analyze Jenkins settings and exposed configurations|Misconfigurations are common attack paths|Weak settings, exposed configuration files|Identify weaknesses|
|**9**|**Plugin Enumeration**|Identify installed plugins and versions|Plugins extend Jenkins functionality and attack surface|Vulnerable Git, SSH, Docker, credential plugins|Check plugin security|
|**10**|**API Enumeration**|Check Jenkins API exposure|APIs may leak information or allow automation access|Users, jobs, system information|Determine exposure level|
|**11**|**Sensitive Data Discovery**|Search for credentials, secrets, and internal information|Jenkins often connects to critical systems|SSH keys, tokens, cloud credentials|Map possible attack paths|
|**12**|**Attack Surface Mapping**|Combine all collected information|Convert findings into a logical attack plan|Vulnerable plugin + exposed job + leaked credential|Move toward vulnerability analysis|
## Common Jenkins Vulnerability Categories
| Vulnerability Type         | Simple Meaning                 | Why It Matters                             |
| -------------------------- | ------------------------------ | ------------------------------------------ |
| Misconfiguration           | Jenkins settings are insecure  | Often easier than exploiting software bugs |
| Weak Permissions           | Users have too much access     | Can lead to privilege abuse                |
| Exposed Secrets            | Credentials are leaked         | Provides access to other systems           |
| Vulnerable Plugins         | Plugin contains security flaw  | Expands attack surface                     |
| Unsafe Build Configuration | Jobs execute dangerous actions | Can lead to system compromise              |
| Authentication Weakness    | Access controls fail           | Unauthorized access                        |
## Vulnerability Assessment
### 1. Jenkins Plugin Vulnerabilities
#### Why Plugins Are Dangerous
Every plugin adds:
- Code
- Permissions
- Features
- Attack surface

#### Plugin Analysis
| Check              | Reason                  |
| ------------------ | ----------------------- |
| Plugin name        | Identify technology     |
| Plugin version     | Compare security status |
| Plugin permissions | Understand impact       |
| Plugin function    | Determine risk          |
### 2. Jenkins Misconfiguration Attacks
#### What is Misconfiguration?
A security setting that was incorrectly configured.

|Misconfiguration|Risk|
|---|---|
|Anonymous access enabled|Information exposure|
|Users can create jobs|Unauthorized automation|
|Users can modify pipelines|Code execution risk|
|Weak permissions|Privilege escalation|
|Exposed configuration|Sensitive data leakage|
### 3. Jenkins Jobs as an Attack Path
#### Why Jobs Matter
A Jenkins job can execute:
```
Code
 ↓
Build Commands
 ↓
System Actions
 ↓
Deployment
```

|Job Element|Security Interest|
|---|---|
|Build script|Commands being executed|
|Pipeline file|Automation logic|
|Environment variables|Possible secrets|
|Repository URL|Internal information|
|Credentials binding|Access tokens|
### 4. Credential Exposure
#### Why Credentials Are Valuable
Jenkins often connects to:
- Servers
- Git repositories
- Cloud platforms
- Deployment systems

|Exposed Data|Possible Impact|
|---|---|
|SSH keys|Remote access|
|API tokens|Account access|
|Database passwords|Data access|
|Cloud credentials|Infrastructure access|

## Troubleshooting Exploitation
| Problem              | Possible Cause           | Solution                     |
| -------------------- | ------------------------ | ---------------------------- |
| Exploit fails        | Wrong version            | Verify exact version         |
| Access denied        | Insufficient permissions | Review user privileges       |
| Plugin exploit fails | Plugin not installed     | Confirm plugin details       |
| Payload fails        | Environment difference   | Analyze target configuration |
| No impact            | Limited permissions      | Find another attack path     |
