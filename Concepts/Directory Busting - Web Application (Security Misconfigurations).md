___

> [!abstract] ## Level 1 - Basics of everything before diving deep!
> > [!abstract] #### What Is Web Application Penetration Testing? 
> > **Web Application Penetration Testing** is the process of legally testing a website or web application to find security weaknesses before attackers find them.
> > 
> > Think of a web application like a house:
> > 
> > | Real World     | Web Application          |
> > | -------------- | ------------------------ |
> > | House address  | Domain/IP address        |
> > | Doors/windows  | Web pages/endpoints      |
> > | Locks          | Authentication systems   |
> > | Hidden rooms   | Hidden directories/files |
> > | Keys           | Passwords/tokens         |
> > | Security flaws | Vulnerabilities          |
> > ##### Web Penetration Testing Workflow
> > - Server Discovery: Identifies the web server.
> > - Content Enumeration: Finds hidden directories/files.
> > - Admin Panel: Discovers an exposed administrative panel.
> > - Credential Access: Tests default credentials.
> > - Unauthorized Access: Gains admin access using default credentials.
> 
> > [!abstract] #### What Is a Web Server?
> > A **web server** is software that receives requests from users and returns web content.
> > 
> > |Web Server|Description|
> > |---|---|
> > |Apache|Popular open-source web server|
> > |Nginx|High-performance web server|
> > |IIS|Microsoft web server|
> > 
> 
> > [!abstract] #### What Is HTTP?
> > > **HTTP (HyperText Transfer Protocol)** is the communication language used between browsers and web servers.
> > 
> > It defines how:
> > - Requests are sent.
> > - Responses are returned.
> > - Web pages are loaded.
> > 
> > Example:
> > Browser sends:
> > ```
> > GET /index.html HTTP/1.1
> > ```
> > 
> > Meaning:
> > > "Server, give me the index.html page."
> > 
> > Server replies:
> > ```
> > HTTP/1.1 200 OK
> > ```
> > 
> > Meaning:
> > > "Request successful."
> > 
> > ##### Important HTTP Methods
> > A method tells the server what action the client wants.
> > 
> > |Method|Purpose|
> > |---|---|
> > |GET|Retrieve data/page|
> > |POST|Send data|
> > |PUT|Upload/update data|
> > |DELETE|Remove data|
> 
> 
> > [!abstract] #### What Is Directory Busting?
> > Directory busting is searching a website for hidden files and folders.
> > 
> > Websites often contain:
> > ```
> > /admin
> > /login
> > /backup
> > /test
> > /uploads
> > ```
> > but these may not appear on the homepage.
> > 
> > A normal user sees:
> > ```
> > website.com
> > ```
> > 
> > A tester searches:
> > ```
> > website.com/admin
> > website.com/backup
> > website.com/uploads
> > ```
> > 
> > ##### What are Hidden Pages & Why do they Exist?
> > ###### Definition
> > Hidden pages are web pages or directories that are **not linked from the main website navigation** but can still be accessed if their URL is discovered.
> > ###### Why they Exist
> > - **Admin Panels:** Manage website settings and users.
> > - **Developer Pages:** Used for testing and debugging.
> > - **Internal Tools:** Support administrative or operational tasks.
> > Example:
> > ```
> > example.com/admin.php
> > ```
> > ###### Security Risks
> > - **Forgotten Pages:** Old pages remain accessible.
> > - **Old Applications:** Outdated software may contain vulnerabilities.
> > - **Default Installations:** Default setup pages or credentials remain active.
> > - **Weak Authentication:** Poor access controls allow unauthorized access.
> > 
> > **Key Point:** Hidden does not mean secure.
> > 
> 
> > [!abstract] #### What Is an Administrative Panel?
> > An administrative panel is a restricted interface used by administrators to control a website.
> > Example:
> > WordPress:
> > ```
> > website.com/wp-admin
> > ```
> > 
> > Admin panels allow:
> > - Uploading content.
> > - Changing settings.
> > - Managing users.
> > 
> > **Administrative panels are normally protected by a login screen**.
> 
> > [!abstract] #### What Is Security Misconfiguration?
> > Security misconfiguration happens when a system is installed or configured incorrectly, creating unnecessary exposure.
> > 
> > Examples:
> > 
> > |Misconfiguration|Risk|
> > |---|---|
> > |Default password|Unauthorized login|
> > |Exposed admin page|Attackers find management interface|
> > |Default installation page|Reveals technology|
> > |Unnecessary services|Larger attack surface|
> > 
> 
> > [!abstract] #### Default Credentials
> > Default credentials are usernames and passwords provided by software manufacturers.
> > 
> > Examples:
> > ```
> > admin:admin
> > admin:password
> > root:root
> > ```
> > 
> > Many administrators forget to change them.
> > Attackers try these first because they are:
> > - Easy.
> > - Common.
> > - Frequently successful.
> 
> > [!abstract] #### The Attack Chain
> > ```
> > 1. Find Target
> >         ↓
> > 2. Scan Services
> >         ↓
> > 3. Identify Web Server
> >         ↓
> > 4. Enumerate Hidden Content
> >         ↓
> > 5. Discover Admin Panel
> >         ↓
> > 6. Test Authentication
> >         ↓
> > 7. Gain Access
> > ```
> 

____

### How Directory Busting Works
The attacker does not know:
```
/admin.php
```
exists.
The website homepage does not link to it.

Example:
Visible website:
```
/
|
├── about
├── contact
└── products
```

Hidden:
```
/
|
├── admin.php
├── backup.zip
└── test/
```

The attacker uses a wordlist.

Example:
```
common.txt
```

Containing:
```
admin
login
backup
test
uploads
```

The tool automatically requests:
```
GET /admin
GET /login
GET /backup
GET /test
```

### Authentication Architecture
```
User
 |
 |
 ↓
Login Form
 |
 |
 ↓
HTTP POST Request
 |
 |
 ↓
Application
 |
 |
 ↓
Database
 |
 |
 ↓
Validation
 |
 |
 ↓
Session Created
```

#### What Happens After Successful Login?
The server creates a session.

Example:
Server sends:
```
Set-Cookie:
session=abc123
```

Browser stores:
```
Cookie:
session=abc123
```

Future requests:
```
GET /admin/dashboard

Cookie:
session=abc123
```
Server says:
"Yes, this user is authenticated."

### Security Misconfiguration Architecture
A properly configured system:
```
Internet

   |
   ↓

Web Server

   |
   ↓

Public Pages Only

   |
   ↓

Protected Admin Area
```

Misconfigured system:
```
Internet

   |
   ↓

Web Server

   |
   |
   +-------- Public Pages
   |
   |
   +-------- Hidden Admin Panel
                  |
                  |
                  ↓
          Default Password
```

The attacker only needs:
1. Discover admin panel.
2. Try default credentials.

___

## Level 2 — Attack Surface & Enumeration
#### **Actual Penetration Testing Sequence**:
```
Target Identification
        ↓
Network Discovery
        ↓
Service Identification
        ↓
Web Technology Identification
        ↓
Web Content Enumeration
        ↓
Manual Verification
        ↓
Vulnerability Hypothesis
```

##### What is Attack Surface?
**Attack surface** means all possible points where an attacker can interact with a system.

For a web server:
```
                Target Server

                     |
        +------------+------------+
        |            |            |
        ↓            ↓            ↓

     HTTP        SSH          FTP
     Port 80     Port 22      Port 21

        |
        ↓

    Web Application

        |
 +------+-------+
 |      |       |
 ↓      ↓       ↓

Login  Upload  API
Page   Feature Endpoint
```
Every exposed service increases attack surface.

### Gobuster
Gobuster is a tool used for discovering:
- Directories.
- Files.
- Subdomains.
- Virtual hosts.

| Type          | Definition                                                                                                                             | Example                                                                |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| Directories   | Folders on a web server that may contain pages or resources.                                                                           | `example.com/admin/`                                                   |
| Files         | Specific files hosted on the web server.                                                                                               | `example.com/config.php`                                               |
| Subdomains    | Separate hostnames under the same main domain.                                                                                         | `dev.example.com`                                                      |
| Virtual Hosts | Multiple websites or subdomains hosted on a single IP address, where the server uses the HTTP `Host` header to serve the correct site. | `target.com` and `internal.target.com` both hosted on IP `10.10.10.50` |
We will use Gobuster for directory busting.
You can study how to use Gobuster in Tools Section.

### Building the Vulnerability Hypothesis
After enumeration:
You have:
```
Technology:
nginx

Discovered:
admin.php

Page:
Login panel

Observation:
Fresh installation
```

Possible hypotheses:
```
1. Default credentials?
2. Weak passwords?
3. Vulnerable application?
4. Misconfiguration?
```

