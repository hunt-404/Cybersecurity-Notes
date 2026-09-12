___
The topic is **how a browser reaches a website, how the `Host` header works, why `/etc/hosts` is sometimes needed, and why tools like Gobuster may need the hostname instead of the IP**.

Let's build it from the ground up in chronological order.

### The big picture

When you type:
```text
http://ignition.htb
```

into your browser, several things happen:
```text
You type a URL
      ↓
Your computer determines the server's IP
      ↓
TCP connection is established
      ↓
HTTP request is sent
      ↓
Request contains Host: ignition.htb
      ↓
Web server receives the request
      ↓
Server chooses the appropriate website
      ↓
Server sends an HTTP response
      ↓
Browser displays the page
```
The important concept is that **the IP address and the hostname have different jobs**.

### What is an IP Address
A computer/server on a network has an IP address.

For example:
```text
10.129.1.27
```
You can think of it as the **network address of the machine**.

So you could potentially access:
```text
http://10.129.1.27
```

The computer can connect to that machine because it knows the IP address.

##### But there's a problem - One server can host multiple websites
Imagine:
```text
10.129.1.27
      │
      ├── ignition.htb
      ├── admin.ignition.htb
      └── dev.ignition.htb
```

All three websites could potentially exist on the **same server/IP**.
So the IP alone isn't enough to tell the web server which website you want.

> That's where the `Host` header becomes important.

### What is the Host header?
When your browser sends an HTTP request, it includes headers.

For example:
```http
GET / HTTP/1.1
Host: ignition.htb
```

The important part is:
```http
Host: ignition.htb
```

This tells the web server:
> "I'm asking for the website called `ignition.htb`."

The server can then decide which website configuration should handle the request.
For example:
```text
Host: ignition.htb
        ↓
Server: ignition.htb website
```

while:
```text
Host: admin.ignition.htb
        ↓
Server: admin website
```

Same server.
Potentially same IP.
Different website.

#### Why can multiple websites share one IP?
This is called **virtual hosting**.

Imagine a physical building:
```text
                  10.129.1.27
                       │
                 ┌─────┴─────┐
                 │   Server  │
                 └─────┬─────┘
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
    ignition.htb  admin.htb    dev.htb
```

The IP gets you to the building.
The hostname tells the web server which "tenant" you're asking for.
This is extremely common on the Internet.

### But how does `ignition.htb` become an IP?
This is where **DNS** comes in.
Normally, when you type:
```text
example.com
```

your computer needs to discover:
```text
example.com → 93.184.216.34
```
DNS is essentially the system that performs that translation.

The general process is:
```text
example.com
     ↓
DNS lookup
     ↓
93.184.216.34
```
Your computer can then connect to that IP.

#### Why local Host mapping is needed?
In a lab, you might be given:
```text
Target IP:
10.129.1.27
```

But the application may expect:
```text
ignition.htb
```
That domain may not exist in public DNS.

So your computer might not know:
```text
ignition.htb → 10.129.1.27
```
You can manually create that mapping using your local `hosts` file.


#### What is the `/etc/hosts` file? ⭐⭐⭐
On Linux, the file is:
```text
/etc/hosts
```
It provides local hostname-to-IP mappings.

For example:
```text
10.129.1.27    ignition.htb
```

This means:
> "On this computer, whenever `ignition.htb` is requested, use `10.129.1.27`."

#### How Local Mapping is Done?
You can edit it with:
```bash
sudo nano /etc/hosts
```

and add:
```text
10.129.1.27    ignition.htb
```
Don't add complete URLs, just the IP and the domain.

Notice that we **don't** put:
```text
http://ignition.htb
```
in the hosts file.

And we don't put:
```text
http://ignition.htb/admin
```
either.

The hosts file maps:
```text
hostname → IP
```
That's all.