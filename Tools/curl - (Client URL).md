___

### What is curl?
**curl** is a command-line tool and software library used to **transfer data between a client and a server using URLs**.

It is mainly used for:
- Sending HTTP/HTTPS requests
- Downloading files
- Uploading files
- Testing APIs
- Debugging web servers
- Checking headers and responses
- Automating network tasks
- Security testing and reconnaissance workflows

###### The curl project contains two major components:
| Component | Purpose                                                               |
| --------- | --------------------------------------------------------------------- |
| `curl`    | Command-line application used from terminal                           |
| `libcurl` | Programming library used by applications to perform network transfers |

`libcurl` is the actual engine behind the curl command. Many applications internally use libcurl for network communication.

Example:
```
curl https://example.com
```

This means:
> "Connect to example.com, request data using the correct protocol, receive the response, and display it."

### Why curl is important in cybersecurity
curl is extremely valuable because it allows security professionals to communicate directly with services without needing a browser.

###### Common security uses
| Security Task          | curl Usage                     |
| ---------------------- | ------------------------------ |
| API testing            | Send GET/POST requests         |
| Web enumeration        | Inspect server responses       |
| Authentication testing | Test login endpoints           |
| Header analysis        | Identify technologies          |
| File transfer testing  | Check upload/download behavior |
| Web debugging          | Analyze HTTP communication     |
| Automation             | Create security scripts        |
Example:
Checking HTTP headers:
```
curl -I https://target.com
```

Possible output:
```
HTTP/2 200
server: nginx
content-type: text/html
```

**Security relevance:**
- Identifies server technology
- Shows cookies
- Reveals security headers
- Helps understand attack surface

## curl High-Level Architecture
The complete curl workflow:
```
              USER
               |
               |
        curl command
               |
               |
        Command Parser
               |
               |
          libcurl Engine
               |
     -----------------------
     |          |          |
 DNS Resolver TLS Layer Protocol Handler
     |          |          |
     -----------------------
               |
          TCP Connection
               |
               |
          Remote Server
               |
          Response Data
               |
               |
          curl Output
```

### curl Internal Components
###### Main Architecture Layers
| Layer                  | Purpose                                 |
| ---------------------- | --------------------------------------- |
| Command Line Interface | Reads user commands                     |
| libcurl                | Main transfer engine                    |
| Protocol Handler       | Handles HTTP, FTP, SMTP etc             |
| Resolver               | Converts domain names into IP addresses |
| Connection Manager     | Creates and manages connections         |
| TLS Engine             | Handles HTTPS encryption                |
| Transfer Engine        | Sends and receives data                 |

The official curl documentation describes libcurl as the engine responsible for transfers, with multiple internal subsystems handling connections, protocols, TLS and other functions.

### How `curl` Processes an HTTP/HTTPS Request
|  Step | Stage                        | What `curl` Does                                          | Example / Flow                                                                                                              | Key Details                                                                                                                                                |
| ----: | ---------------------------- | --------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1** | **Command Parsing**          | Reads and interprets the command-line arguments.          | `curl -H "Authorization: Bearer TOKEN" https://api.site.com`                                                                | **URL:** `https://api.site.com`<br>**Header:** `Authorization`<br>**Method:** `GET`                                                                        |
| **2** | **URL Analysis**             | Breaks the URL into its individual components.            | `https://example.com/page?id=10`                                                                                            | **Protocol:** HTTPS<br>**Host/Domain:** `example.com`<br>**Path:** `/page`<br>**Query:** `id=10`<br>**Port:** `443`                                        |
| **3** | **DNS Resolution**           | Converts the domain name into an IP address.              | `example.com` → DNS Lookup → `93.184.216.34`                                                                                | **Flow:** `curl → DNS Resolver → DNS Server → IP Address`<br><br>DNS information can reveal **hosting providers, subdomains, and infrastructure details**. |
| **4** | **Connection Establishment** | Establishes the network connection to the server.         | `curl → TCP Handshake → TLS Handshake → Encrypted Channel → HTTP Request`                                                   | **TCP 3-way handshake:**<br>Client → `SYN` → Server<br>Client ← `SYN-ACK` ← Server<br>Client → `ACK` → Server                                              |
| **5** | **TLS Encryption (HTTPS)**   | Negotiates a secure encrypted connection with the server. | Client → `Hello` → Server<br>Client ← `Certificate` ← Server<br>Client → `Key Exchange` → Server<br>→ **Encrypted Channel** | **Encryption:** Prevents data from being read<br>**Authentication:** Verifies server identity<br>**Integrity:** Prevents undetected modification           |
| **6** | **HTTP Request Creation**    | Constructs and sends the HTTP request.                    | See request example below.                                                                                                  | **Method:** `GET`<br>**Path:** `/index.html`<br>**Headers:** Metadata/control information<br>**Body:** Optional request data                               |
| **7** | **Server Response**          | Receives and processes the server's HTTP response.        | See response example below.                                                                                                 | **Status Code:** Success/failure<br>**Headers:** Response metadata<br>**Body:** Actual response content                                                    |

### curl vs Browser
| Feature          | Browser  | curl      |
| ---------------- | -------- | --------- |
| GUI              | Yes      | No        |
| Automation       | Limited  | Excellent |
| API testing      | Manual   | Excellent |
| Headers control  | Limited  | Full      |
| Scripts          | Limited  | Excellent |
| Security testing | Moderate | Strong    |

### Simple Mental Model
Think of curl as:
```
Browser without the GUI
+
Developer tools
+
Automation engine
+
Network debugging tool
```

___

# curl Commands Section

## Important curl Flags / Options
| Flag / Option       | Purpose                                                                                 |
| ------------------- | --------------------------------------------------------------------------------------- |
| `-v`                | Verbose mode; shows DNS lookup, connection, TLS handshake, request and response details |
| `-I`                | Sends a HEAD request and displays only HTTP headers                                     |
| `-i`                | Includes response headers along with the body                                           |
| `-L`                | Automatically follows HTTP redirects                                                    |
| `-X`                | Defines a custom HTTP method (GET, POST, PUT, DELETE, etc.)                             |
| `-H`                | Adds custom HTTP headers                                                                |
| `-d` / `--data`     | Sends data inside a POST request body                                                   |
| `-F`                | Sends multipart form data (file uploads/forms)                                          |
| `-u`                | Sends HTTP Basic Authentication credentials                                             |
| `-A`                | Changes the User-Agent value                                                            |
| `-o`                | Saves output to a specific filename                                                     |
| `-O`                | Downloads file using the original remote filename                                       |
| `-s`                | Silent mode; hides progress information                                                 |
| `-k`                | Skips TLS certificate verification (testing only)                                       |
| `-c`                | Saves received cookies into a file                                                      |
| `-b`                | Sends stored cookies with requests                                                      |
| `-x`                | Uses a proxy server                                                                     |
| `--proxy`           | Defines proxy connection                                                                |
| `--trace`           | Creates a complete communication trace                                                  |
| `--trace-ascii`     | Creates readable request/response debugging logs                                        |
| `--retry`           | Automatically retries failed requests                                                   |
| `--connect-timeout` | Limits connection waiting time                                                          |
| `--limit-rate`      | Controls transfer speed                                                                 |
| `--http2`           | Forces HTTP/2 communication                                                             |
| `-w`                | Prints custom output information such as status codes                                   |
| `-T`                | Uploads a file to a server                                                              |

## Beginner Commands — Basic Discovery & Usage

> **Focus:** Basic discovery, identification, connectivity checks, downloading, and understanding server responses.

|Purpose|Command|Example|One-Liner Explanation|
|---|---|---|---|
|Basic website/request testing|`curl <URL>`|`curl https://example.com`|Sends a basic GET request and displays the server response body.|
|Inspect HTTP headers|`curl -I <URL>`|`curl -I https://example.com`|Retrieves only response headers to identify status codes, cookies, redirects, and server details.|
|View headers with response body|`curl -i <URL>`|`curl -i https://example.com`|Displays both HTTP headers and the actual returned content.|
|Debug complete connection|`curl -v <URL>`|`curl -v https://example.com`|Shows DNS resolution, TCP connection, TLS negotiation, request, and response information.|
|Follow website redirects|`curl -L <URL>`|`curl -L https://example.com`|Automatically follows HTTP 301/302 redirects until reaching the final page.|
|Save response to a file|`curl -o <FILE> <URL>`|`curl -o page.html https://example.com`|Stores the server response in a custom filename for later analysis.|
|Download file with original name|`curl -O <URL>/<FILE>`|`curl -O https://example.com/manual.pdf`|Downloads a remote file while preserving its original filename.|
|Run curl silently|`curl -s <URL>`|`curl -s https://example.com`|Removes progress information and returns only the response data.|
|Check curl installation|`curl --version`|`curl --version`|Displays curl version, supported protocols, and enabled features.|
|Change client identity|`curl -A "<USER_AGENT>" <URL>`|`curl -A "Mozilla/5.0" https://example.com`|Changes the User-Agent header to simulate another browser or client.|
|Test connection timeout|`curl --connect-timeout <SECONDS> <URL>`|`curl --connect-timeout 5 https://example.com`|Limits the time curl waits while establishing a connection.|
|Check HTTP status code|`curl -w "%{http_code}" -o /dev/null <URL>`|`curl -w "%{http_code}" -o /dev/null https://example.com`|Extracts only the HTTP status code for quick availability checks.|
|Measure request time|`curl -w "%{time_total}" -o /dev/null <URL>`|`curl -w "%{time_total}" -o /dev/null https://example.com`|Displays total request completion time for performance testing.|
|Force IPv4 connection|`curl -4 <URL>`|`curl -4 https://example.com`|Forces communication through IPv4 instead of IPv6.|
|Force IPv6 connection|`curl -6 <URL>`|`curl -6 https://example.com`|Forces communication through IPv6 when available.|
## curl Command Reference — Intermediate Level
| Purpose                          | Command                                                              | Example                                                                                                | One-Liner Explanation                                                       |
| -------------------------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| Send custom HTTP method          | `curl -X <METHOD> <URL>`                                             | `curl -X OPTIONS https://example.com`                                                                  | Sends specific HTTP methods such as GET, POST, PUT, DELETE, or OPTIONS.     |
| Add custom HTTP headers          | `curl -H "<HEADER>: <VALUE>" <URL>`                                  | `curl -H "Accept: application/json" https://api.example.com/users`                                     | Adds custom headers required for API communication and testing.             |
| Send POST request data           | `curl -X POST -d "<DATA>" <URL>`                                     | `curl -X POST -d "username=test" https://example.com/login`                                            | Sends form data to a server using an HTTP POST request.                     |
| Send JSON API request            | `curl -X POST -H "Content-Type: application/json" -d '<JSON>' <URL>` | `curl -X POST -H "Content-Type: application/json" -d '{"user":"admin"}' https://api.example.com/login` | Sends structured JSON data to REST APIs.                                    |
| Test Basic Authentication        | `curl -u <USERNAME>:<PASSWORD> <URL>`                                | `curl -u admin:password https://example.com/admin`                                                     | Sends username and password credentials using HTTP Basic Authentication.    |
| Send Bearer authentication token | `curl -H "Authorization: Bearer <TOKEN>" <URL>`                      | `curl -H "Authorization: Bearer ey123456" https://api.example.com/profile`                             | Sends token-based authentication used by modern APIs.                       |
| Send API key header              | `curl -H "<API_HEADER>: <KEY>" <URL>`                                | `curl -H "X-API-Key: abc123" https://api.example.com/data`                                             | Tests API access using custom authentication headers.                       |
| Save cookies from server         | `curl -c <COOKIE_FILE> <URL>`                                        | `curl -c cookies.txt https://example.com/login`                                                        | Stores cookies received from a server into a local file.                    |
| Use stored cookies               | `curl -b <COOKIE_FILE> <URL>`                                        | `curl -b cookies.txt https://example.com/dashboard`                                                    | Sends previously saved cookies to maintain a session.                       |
| Send form data                   | `curl -F "<FIELD>=<VALUE>" <URL>`                                    | `curl -F "username=test" https://example.com/login`                                                    | Submits multipart form data similar to a browser form.                      |
| Upload form file                 | `curl -F "<FIELD>=@<FILE>" <URL>`                                    | `curl -F "file=@report.txt" https://example.com/upload`                                                | Uploads a file through a multipart form request.                            |
| Check allowed HTTP methods       | `curl -X OPTIONS -i <URL>`                                           | `curl -X OPTIONS -i https://example.com`                                                               | Checks which HTTP methods a server accepts.                                 |
| Inspect server technology        | `curl -I <URL>`                                                      | `curl -I https://example.com`                                                                          | Collects server headers that may reveal software and configuration details. |
| Store API response               | `curl <URL> > <FILE>`                                                | `curl https://api.example.com/users > users.json`                                                      | Saves API output into a local file for processing.                          |
| Test different ost headers       | `curl -H "Host: <DOMAIN>" <IP>`                                      | `curl -H "Host: test.example.com" http://192.168.1.10`                                                 | Sends requests with a custom Host header for virtual host testing.          |

## curl Command Reference — Advanced Level
| Purpose                             | Command                                     | Example                                                                    | One-Liner Explanation                                                   |
| ----------------------------------- | ------------------------------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Create detailed communication trace | `curl --trace <FILE> <URL>`                 | `curl --trace debug.txt https://example.com`                               | Records complete request and response communication for deep debugging. |
| Create readable trace logs          | `curl --trace-ascii <FILE> <URL>`           | `curl --trace-ascii trace.txt https://example.com`                         | Creates human-readable network transaction logs.                        |
| Use proxy server                    | `curl -x <PROXY>:<PORT> <URL>`              | `curl -x 127.0.0.1:8080 https://example.com`                               | Routes requests through a proxy for controlled analysis.                |
| Use SOCKS proxy                     | `curl --socks5 <IP>:<PORT> <URL>`           | `curl --socks5 127.0.0.1:9050 https://example.com`                         | Sends traffic through a SOCKS5 proxy connection.                        |
| Force HTTP/2 communication          | `curl --http2 <URL>`                        | `curl --http2 https://example.com`                                         | Tests and analyzes HTTP/2 protocol behavior.                            |
| Force HTTP/1.1 communication        | `curl --http1.1 <URL>`                      | `curl --http1.1 https://example.com`                                       | Forces older HTTP protocol communication for compatibility testing.     |
| Test TLS version                    | `curl --tlsv1.3 <URL>`                      | `curl --tlsv1.3 https://example.com`                                       | Forces a specific TLS version during HTTPS negotiation.                 |
| Ignore TLS certificate errors       | `curl -k <HTTPS_URL>`                       | `curl -k https://example.com`                                              | Skips certificate verification for controlled testing environments.     |
| Retry failed requests               | `curl --retry <NUMBER> <URL>`               | `curl --retry 5 https://example.com`                                       | Automatically retries failed network requests.                          |
| Limit transfer speed                | `curl --limit-rate <RATE> <URL>`            | `curl --limit-rate 500K https://example.com/file.zip`                      | Controls bandwidth usage during transfers.                              |
| Upload files directly               | `curl -T <FILE> <URL>`                      | `curl -T backup.zip https://example.com/upload`                            | Sends a file using an upload request.                                   |
| Perform multiple transfers          | `curl <URL1> <URL2>`                        | `curl https://example.com/a https://example.com/b`                         | Processes multiple URLs in a single execution.                          |
| Parallel downloads                  | `curl --parallel <URL1> <URL2>`             | `curl --parallel https://example.com/a.zip https://example.com/b.zip`      | Downloads multiple resources simultaneously.                            |
| Extract custom response data        | `curl -w "<FORMAT>" <URL>`                  | `curl -w "Status:%{http_code}" https://example.com`                        | Prints selected response statistics useful for automation.              |
| Use certificate authentication      | `curl --cert <CERT_FILE> <URL>`             | `curl --cert client.pem https://secure.example.com`                        | Uses client certificates for mutual TLS authentication.                 |
| Use custom DNS resolution           | `curl --resolve <DOMAIN>:<PORT>:<IP> <URL>` | `curl --resolve test.example.com:443:10.10.10.50 https://test.example.com` | Maps a domain to a specific IP without changing DNS.                    |
| Connect through specific interface  | `curl --interface <INTERFACE> <URL>`        | `curl --interface eth0 https://example.com`                                | Forces traffic through a selected network interface.                    |
| Run automated API checks            | `curl <OPTIONS> <URL> > <OUTPUT_FILE>`      | `curl -s https://api.example.com/status > result.json`                     | Automates repeated requests and stores results for analysis.            |
