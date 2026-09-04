___
## Web Application Architecture 
A web application is a chain of multiple systems communicating together.

High-level architecture:
```
              Internet
                  |
                  |
          User Browser
        (Chrome/Firefox)
                  |
                  |
          HTTP Request
                  |
                  ↓
        +----------------+
        |  Web Server    |
        |  Nginx/Apache  |
        +----------------+
                  |
                  |
          Application Code
        (PHP/Python/Java)
                  |
                  |
              Database
        (MySQL/PostgreSQL)
```

### Detailed Breakdown
#### Phase 1: DNS Resolution (Finding the Address)
| **Action**      | **Description**                                                         | **Analogy**                                                           | **Example**                                |
| --------------- | ----------------------------------------------------------------------- | --------------------------------------------------------------------- | ------------------------------------------ |
| **Input**       | User types a URL into the browser.                                      | -                                                                     | `[http://example.com](http://example.com)` |
| **Translation** | Domain Name System (DNS) translates the domain name into an IP address. | Looking up a person's name in a phone directory to find their number. | `example.com` → `93.184.216.34`            |
#### **Phase 2: TCP Connection (The Three-Way Handshake)**
| **Step** | **Message**   | **Direction**   | **Meaning**                                        |
| -------- | ------------- | --------------- | -------------------------------------------------- |
| **1**    | **SYN**       | Client → Server | "I want to start communication."                   |
| **2**    | **SYN + ACK** | Server → Client | "I received your request and I am ready."          |
| **3**    | **ACK**       | Client → Server | "Good, let's communicate. Connection established." |
#### Phase 3: TLS/SSL Handshake (Securing the Connection)
| **Step** | **Action**              | **Direction**   | **Meaning**                                                                      |
| -------- | ----------------------- | --------------- | -------------------------------------------------------------------------------- |
| **1**    | **Client Hello**        | Client → Server | "Let's connect securely. Here are the encryption methods I support."             |
| **2**    | **Server Hello & Cert** | Server → Client | "Here is my digital ID (certificate) and the encryption method we will use."     |
| **3**    | **Key Exchange**        | Both            | Both sides securely generate a shared session key to encrypt all following data. |
> Only if client is using HTTPS. Simple HTTP will omit this step.
#### Phase 4: HTTP Request (Asking for Data)
| **Component**   | **Example**         | **Meaning**                                                                  |
| --------------- | ------------------- | ---------------------------------------------------------------------------- |
| **Method**      | `GET`               | The action you want to take (e.g., retrieving a file from the server).       |
| **Resource**    | `/admin.php`        | The exact file or path being requested.                                      |
| **Protocol**    | `HTTP/1.1`          | "We are communicating using HTTP version 1.1."                               |
| **Host Header** | `Host: 10.10.10.50` | Identifies which specific website to load, since one IP can host many sites. |
#### Phase 5: Web Server Processing (Locating the File)
| **URL Requested**                                      | **Internal Action**                                                    | **Server File Path**      | **Outcome**                                                      |
| ------------------------------------------------------ | ---------------------------------------------------------------------- | ------------------------- | ---------------------------------------------------------------- |
| `[target.com/admin.php](https://target.com/admin.php)` | The server maps the requested URL to its internal directory structure. | `/var/www/html/admin.php` | If found: Prepare to return it. If missing: Prepare a 404 error. |
#### Phase 6: HTTP Response (Sending Data Back)
| **Status Code** | **Meaning**  | **Result**                                                                         |
| --------------- | ------------ | ---------------------------------------------------------------------------------- |
| **200 OK**      | Success      | The file exists. The server sends back the content (e.g., `<html>Welcome</html>`). |
| **301**         | Redirect     | The resource moved; the server tells the browser to go to a new URL.               |
| **403**         | Forbidden    | The server understands the request but refuses to authorize it.                    |
| **404**         | Not Found    | The server checked its directory, and the file does not exist.                     |
| **500**         | Server Error | Something failed internally on the web server.                                     |

