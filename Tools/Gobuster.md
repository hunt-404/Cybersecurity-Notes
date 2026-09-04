___
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
#### Commands Used by Gobuster
| **Purpose**                  | **Gobuster Command**                                                              | **Functional Breakdown**                             |
| ---------------------------- | --------------------------------------------------------------------------------- | ---------------------------------------------------- |
| **Directory Enumeration**    | `gobuster dir -u http://target.com -w wordlist.txt`                               | Finds hidden directories and files.                  |
| **Directory + Extensions**   | `gobuster dir -u http://target.com -w wordlist.txt -x php,txt,html`               | Appends specific file extensions.                    |
| **Specify Threads**          | `gobuster dir -u http://target.com -w wordlist.txt -t 50`                         | Sets concurrent threads for scan speed.              |
| **Save Results**             | `gobuster dir -u http://target.com -w wordlist.txt -o results.txt`                | Saves scan output to a file.                         |
| **Verbose Output**           | `gobuster dir -u http://target.com -w wordlist.txt -v`                            | Shows all request attempts (including errors).       |
| **Status Code Filtering**    | `gobuster dir -u http://target.com -w wordlist.txt -s 200,301,302`                | Includes only specific HTTP status codes.            |
| **Exclude Status Codes**     | `gobuster dir -u http://target.com -w wordlist.txt -b 404`                        | Excludes specific HTTP status codes (e.g., 404).     |
| **Subdomain Enumeration**    | `gobuster dns -d target.com -w subdomains.txt`                                    | Discovers subdomains via DNS queries.                |
| **Wildcard DNS Check**       | `gobuster dns -d target.com -w subdomains.txt --wildcard`                         | Bypasses wildcard DNS catch-all false positives.     |
| **Virtual Host Enumeration** | `gobuster vhost -u [http://target.com](http://target.com) -w vhosts.txt`          | Finds subdomains via HTTP Host header brute-forcing. |
| **Use a Proxy**              | `gobuster dir -u http://target.com -w wordlist.txt --proxy http://127.0.0.1:8080` | Routes traffic through a proxy (e.g., Burp Suite).   |
#### Gobuster Modes & Flags Breakdown
| **Flag / Mode**  | **Argument**     | **Description**                                                                                                                 |
| ---------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **`dir`**        | _Mode_           | Uses HTTP requests to brute-force directories and files on a web server.                                                        |
| **`dns`**        | _Mode_           | Uses DNS queries to brute-force subdomains for a given domain.                                                                  |
| **`vhost`**      | _Mode_           | Uses HTTP Host headers to brute-force virtual hosts on the same IP.                                                             |
| **`-u`**         | `<URL>`          | The target URL (used in `dir` and `vhost` modes).                                                                               |
| **`-w`**         | `<file_path>`    | The path to the wordlist file used for the brute-force attack.                                                                  |
| **`-d`**         | `<domain>`       | The target domain name (used in `dns` mode).                                                                                    |
| **`-x`**         | `<extensions>`   | File extensions to append to each wordlist item (e.g., `php,txt,html`).                                                         |
| **`-t`**         | `<number>`       | Number of concurrent threads to use (higher = faster, but noisier).                                                             |
| **`-o`**         | `<file_path>`    | The destination file to save the scan results.                                                                                  |
| **`-v`**         | _None_           | Enables verbose mode to show detailed request/response information.                                                             |
| **`-s`**         | `<status_codes>` | A comma-separated list of HTTP status codes to **include** in results.                                                          |
| **`-b`**         | `<status_codes>` | A comma-separated list of HTTP status codes to **exclude** (blacklist) from results.                                            |
| **`--wildcard`** | _None_           | Forces the scan to continue even if a wildcard DNS configuration is detected.                                                   |
| **`--proxy`**    | `<URL>`          | Routes the scanner's traffic through a specified proxy (e.g., `[http://127.0.0.1:8080](http://127.0.0.1:8080)` for Burp Suite). |
