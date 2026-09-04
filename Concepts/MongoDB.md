___
# Level 1 - Fundamentals
## What Is MongoDB?
**MongoDB** is a type of database system used to store, manage, and retrieve data.
Unlike traditional databases that store information in tables and rows, MongoDB stores data in a flexible format called **documents**.

Simple example:

A traditional SQL database might store a user like this:

| ID  | Name  | Email            | Age |
| --- | ----- | ---------------- | --- |
| 1   | Alice | `alice@test.com` | 25  |

MongoDB stores the same user as a document:
```JSON
{
  "name": "Alice",
  "email": "alice@test.com",
  "age": 25
}
```
This looks similar to a programming language object.

#### Why Does MongoDB Exist?
Traditional databases (like MySQL) are powerful but have a fixed structure.
Example:
A company stores customer data:
```
Customer Table

ID | Name | Email | Phone
--------------------------
1  | Bob  | x@y.com | 12345
```

Later they want to add:
```
Social Media Accounts
Preferences
Multiple Addresses
Purchase History
```

Traditional databases require:
- Creating new tables
- Changing database structure
- Managing complex relationships

MongoDB was designed to make handling changing and large-scale data easier.

#### Problems MongoDB Solved
###### **1. Flexible Data Storage**
Data structures can change easily.

Example:
User 1:
```JSON
{
"name":"John",
"age":30
}
```

User 2:
```JSON
{
"name":"Sarah",
"age":25,
"skills":["Python","Security"]
}
```
Both can exist without changing the database design.
###### 2. Handling Large Amounts of Data
MongoDB is designed for:
- Large applications
- High traffic websites
- Real-time systems

Examples:
- Social media platforms
- E-commerce websites
- IoT systems
###### 3. Fast Development
Developers can store application data in a format close to how applications already represent objects.

Example:
Application object:
```python
user = {
"name":"Alex",
"role":"admin"
}
```

MongoDB document:
```JSON
{
"name":"Alex",
"role":"admin"
}
```
Less conversion work is needed.

## Components of MongoDB
MongoDB has a hierarchy:
```
MongoDB Server
       |
       |
   Database
       |
       |
   Collection
       |
       |
   Document
       |
       |
     Field
```
Let's understand each part.

#### Database
A MongoDB database contains collections that store related documents.

Example:
```
shop_database
|
|-- Customers
|-- Products
|-- Orders
```
#### Collection
A collection is similar to a table in SQL databases.

Example:
SQL:
```
Users Table
```

MongoDB:
```JSON
Users Collection

Document 1
Document 2
Document 3
```
#### Document
A document is a single record.

Example:
One customer:
```JSON
{
"name":"John",
"email":"john@test.com",
"role":"admin"
}
```
#### Field
A field is a piece of information inside a document.

Example:
```JSON
{
"name":"John",
"age":25
}
```

Fields:
```
name
age
```

___

## Normal Working
Now let's understand how MongoDB works when everything is configured correctly.
### Basic MongoDB Communication Flow
```
User/Application
        |
        |
        v
MongoDB Driver
        |
        |
        v
MongoDB Server
        |
        |
        v
Database Storage
```

| Step | Component | Action | What Happens | Example | Output |
|---|---|---|---|---|---|
| **1** | **Application** | Sends request | The application asks MongoDB for specific data. | Find user where `username = "admin"` | A database query/request |
| **2** | **MongoDB Driver** | Translates & sends request | The driver converts the application's request into a format MongoDB understands and sends it to the server. | Python app → Python MongoDB Driver | Request sent to MongoDB |
| **3** | **MongoDB Server** | Processes request | MongoDB receives the request and checks authentication/permissions. | Checks whether the application can access the `users` collection | Request is authorized |
| **4** | **MongoDB Server** | Searches database | MongoDB searches the required collection and documents. | Searches `users` for `username: "admin"` | Matching document found |
| **5** | **MongoDB Server** | Returns result | MongoDB sends the matching data back through the driver. | `{ "username": "admin", "role": "administrator" }` | Query result |
| **6** | **MongoDB Driver** | Delivers result | The driver converts the response into a format the application can work with. | BSON → application-friendly object | Data available to application |
| **7** | **Application** | Uses the data | The application uses the returned information. | Displays admin details on a website | Information shown to the user |
___
## Common Beginner Mistakes
| Mistake                                   | Clarification                                                                                                                       |
| ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| **MongoDB is just "JSON storage"**        | MongoDB is a complete database system with authentication, permissions, networking, storage, indexing, and query processing.        |
| **Jumping directly into MongoDB attacks** | A professional first learns how MongoDB works, communicates, authenticates, and processes requests before studying security issues. |
| **Confusing MongoDB with SQL databases**  | **SQL:** Database → Table → Row → Column<br>**MongoDB:** Database → Collection → Document → Field                                   |
___
## Common MongoDB Security Problems

#### 1. Exposed Database
MongoDB server accidentally accessible from the internet.

Bad:
```
Internet
    |
    |
    v
MongoDB
```
Anyone may attempt interaction.
#### 2. Weak Authentication
Poor username/password protection.
Risk:
Unauthorized users may access data.
#### 3. Incorrect Permissions
Application user has too many privileges.

Bad:
```
Application User
        |
        |
        v
Full Database Control
```
#### 4. Misconfiguration
- Default settings left unchanged
- No network restrictions
- Missing access controls

### Attacker Mindset
A penetration tester asks:
```
Is MongoDB running?

       |
       v

Can I communicate with it?

       |
       v

Is authentication enabled?

       |
       v

What access level exists?

       |
       v

What data is exposed?
```
___
# LEVEL 2: Deep Architecture & Internal Mechanics
###### How does MongoDB actually work internally?
A simplified internal view:
```
                 Client Application
                         |
                         |
                  MongoDB Driver
                         |
                         |
                  Network Connection
                         |
                         |
                  MongoDB Server
                         |
        --------------------------------
        |                              |
 Query Processing                 Storage Engine
        |                              |
 Authentication                 Data Files
        |
 Authorization
```


>First understand the deployment architecture → then understand what happens inside a MongoDB server → then understand security.
## MongoDB Deployment
Before understanding how MongoDB works internally, we must understand:
	
**"How is MongoDB arranged in real environments?"**

A MongoDB deployment is the way MongoDB servers are organized to provide:
- Data storage
- Availability
- Performance
- Scalability

In real companies, MongoDB is rarely just:
```
Application → One MongoDB Server
```

Instead, it is usually:
```
Client Applications
          |
          |
   MongoDB Deployment
          |
          |
 Multiple MongoDB Components
```
###### Why Does MongoDB Need Different Deployment Models?
For a **small application**, a single MongoDB server may be enough.
```text
Application
     |
   mongod
     |
  Database
```

For a **large application**, problems can arise:
- **Server Failure:** If the server crashes, the application may stop.
- **Too Much Data:** One server may not handle storage and query requirements.
- **Too Many Requests:** High traffic can make one server a bottleneck.

**MongoDB solves these problems using different deployment architectures.**
### MongoDB Deployment Types
MongoDB has three major deployment models:
```
                    MongoDB Deployments


                         |
        +----------------+----------------+

        |                |                |

  Standalone       Replica Set      Sharded Cluster
```

#### 1. Standalone Deployment
A **standalone deployment** is a single MongoDB server running independently, with **no replication, clustering, or distribution**.

###### **Architecture**
```text
Application
     |
   mongod
     |
  Database
     |
    Disk
```
###### Normal Operation
```text
User → Application → mongod → Save Data → Response
```

| Advantages | Disadvantages |
|---|---|
| **Simple** | No high availability |
| **Easy to configure** | Server failure makes data unavailable |
| **Good for testing** | Not suitable for critical production systems |
#### 2. Replica Set Deployment
A **replica set** is a group of MongoDB servers that maintain copies of the same data. It solves the **single-server failure** problem.
###### Architecture
```text
             Application
                  |
               Primary
              /       \
       Secondary    Secondary
```

| Component | Role |
|---|---|
| **Primary** | Accepts writes and maintains the latest data |
| **Secondary** | Copies data from the primary and can participate in failover |
###### Write & Replication Flow
```text
Application → Primary → Replication → Secondaries
```
The application sends a write to the **primary**, which records the change and replicates it to the secondaries.
###### Read Flow
Reads can be configured to use:

- **Primary** — normal/default approach
- **Secondary** — can help distribute read load
###### Automatic Failover
If the primary fails, the remaining members hold an **election** to choose a new primary.

>**Main benefit:** High availability — the system can continue operating after a primary failure.

#### 3. Sharded Cluster Deployment
**Sharding** solves the problem of a single replica set not being able to handle very large amounts of data or traffic.

It means **dividing data across multiple servers (shards)**.

> Example
```text
Server 1 → Users A–F
Server 2 → Users G–M
Server 3 → Users N–Z
```

###### Sharded Cluster Architecture
```text
          Client Applications
                  |
                mongos
             Query Router
                  |
        +---------+---------+
        |                   |
 Config Servers          Shards
                            |
                 +----------+----------+
                 |                     |
              Shard 1               Shard 2
            (Replica Set)         (Replica Set)
```

###### Main Components
| Component          | Role                                                                                                                  |
| ------------------ | --------------------------------------------------------------------------------------------------------------------- |
| **mongos**         | Acts as the **query router**. Receives requests, identifies the correct shard, sends the query, and combines results. |
| **Config Servers** | Store **cluster metadata**, such as which data belongs to which shard.                                                |
| **Shards**         | Store the **actual application data**.                                                                                |
| **Replica Set**    | A shard is usually a **replica set**, providing redundancy and high availability.                                     |
###### Simple Mental Model
- **mongos** → Reception desk: _"Where should this request go?"_
- **Config Servers** → Map: _"Where is the data?"_
- **Shards** → Storage sections: _"Here is the actual data."_
### mongos (Imp Concept)
`mongos` is a router.

It is used when MongoDB is distributed across multiple servers.
This setup is called **Sharding**.

Example:
Large company database:
```
                Application

                    |
                    |

                  mongos

              /      |      \

        MongoDB   MongoDB   MongoDB
        Server A  Server B  Server C
```

The application talks to one place.
`mongos` decides where the data actually lives.
___
## Internal Working Architecture
A useful internal model is:
```
Application / mongosh
        |
        | MongoDB Driver
        v
+-----------------------+
| TCP connection        |
| optional TLS          |
+-----------------------+
        |
        | MongoDB Wire Protocol
        | OP_MSG + BSON
        v
+-----------------------+
|       mongod          |
|                       |
|  Network handling     |
|       ↓               |
|  Authentication       |
|       ↓               |
|  Authorization        |
|       ↓               |
|  Command processing   |
|       ↓               |
|  Query planner        |
|       ↓               |
|  Query execution      |
|       ↓               |
|  WiredTiger           |
+-----------------------+
        |
    +---+---+
    |       |
    v       v
  RAM      Disk
 Cache   .wt / journal
```

The layers matter because later you will ask different security questions at each one:

```
Network        → Can I reach it?
Protocol       → Is it really MongoDB?
Authentication → Who can log in?
Authorization  → What can that identity do?
Database       → What data exists?
Storage        → Where is that data on the host?
Cluster        → What other MongoDB systems exist?
```

### Core Concepts
#### 1. mongosh
`mongosh` means **MongoDB Shell**.
It is a **client**, not the database server.
```
mongosh
   |
   | MongoDB protocol
   v
 mongod
```
You type commands into `mongosh`; it sends database requests to `mongod`.

```
mongod   = database server
mongosh  = modern client shell
mongo    = older client shell
```
#### 2. mongod
`mongod` is the main MongoDB database server.

It is the program that actually handles:
- client connections
- authentication
- authorization
- queries
- writes
- indexes
- storage
- replication
- database administration

Think of it as:
> The engine running the database.

Without `mongod`, MongoDB does not exist.

When you start MongoDB:
```
Operating System
        |
        |
        v
     mongod process
        |
        |
        v
 MongoDB database available
```

`mongod` is a daemon process.
###### Daemon
A background program that runs continuously.
#### 3. Query Planner
Suppose the application asks:
```JSON
{ username: "alice" }
```

MongoDB needs to decide:

> "What is the cheapest way to find Alice?"
###### 1. Collection Scan `COLLSCAN` - Possibility A:
```
Read document 1
Read document 2
Read document 3
...
Read every document
```
This is a **collection scan** `COLLSCAN`.
###### 2. Index Scan `IXSCAN` - Possibility B
```
Use username index
       ↓
Find "alice"
       ↓
Retrieve matching document
```
This is a **index scan** `IXSCAN`.
#### 4. Storage Engine/WiredTiger
**WiredTiger** is the component that actually manages MongoDB's data in memory and on disk.

MongoDB needs something responsible for:
- reading stored data
- writing data
- caching data
- coordinating concurrent operations
- maintaining indexes
- compression
- crash recovery

>That component is the **storage engine**.
  Modern MongoDB uses WiredTiger by default.

### Internal Working - Main
The full conceptual path is:
![[Pasted image 20260902061116.png]]

###### Workflow from Application to MongoDB
| S No. | Step                  | Crisp explanation                    |
| ----: | --------------------- | ------------------------------------ |
|     1 | Application           | Sends the database request.          |
|     2 | MongoDB Driver        | Converts it into a MongoDB command.  |
|     3 | BSON Command          | Encodes the request in BSON.         |
|     4 | TCP / TLS             | Transports the request securely.     |
|     5 | Wire Protocol         | Defines client-server communication. |
|     6 | mongod                | Receives and processes the request.  |
|     7 | Parse Command         | Interprets the requested operation.  |
|     8 | Authenticate Identity | Verifies the user or client.         |
|     9 | Check Authorization   | Checks access permissions.           |
|    10 | Query Planner         | Selects the best query path.         |
|    11 | COLLSCAN              | Scans the full collection.           |
|    12 | IXSCAN                | Searches using an index.             |
|    13 | Retrieve Document     | Fetches matching documents.          |
|    14 | WiredTiger            | Manages data storage and retrieval.  |
|    15 | WT Cache              | Serves cached data from memory.      |
|    16 | Filesystem / Disk     | Reads data from storage when needed. |
|    17 | Result                | Builds the query output.             |
###### Workflow from MongoDB to Application
| S No. | Step           | Crisp explanation                                |
| ----: | -------------- | ------------------------------------------------ |
|     1 | Result         | MongoDB prepares the query result.               |
|     2 | BSON Response  | Encodes the result into BSON.                    |
|     3 | Wire Protocol  | Packages the response for MongoDB communication. |
|     4 | TCP / TLS      | Sends the response back over the network.        |
|     5 | MongoDB Driver | Decodes BSON into application-friendly data.     |
|     6 | Application    | Receives and uses the final result.              |
### Common Misconceptions
|   # | Common Mistake                                      | Correct Mental Model                                                                         | Key Takeaway                                                   |
| --: | --------------------------------------------------- | -------------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
|   1 | Confusing **replication** and **sharding**          | Replication = same data on multiple nodes. Sharding = different data portions across shards. | A shard is often itself a replica set.                         |
|   2 | Confusing **`mongod`** and **`mongos`**             | `mongod` stores/manages data; `mongos` routes traffic in a sharded cluster.                  | `mongod` = database daemon, `mongos` = router.                 |
|   3 | Thinking every query touches disk                   | Query → cache → disk only if needed.                                                         | Many reads are served from cache.                              |
|   4 | Thinking authentication is the first network packet | TCP → optional TLS → MongoDB handshake → authentication → commands.                          | Authentication happens after connection setup and handshake.   |
|   5 | Assuming one connection means one server            | Initial endpoint → `hello` response → topology discovery.                                    | A client can learn about other replica set or cluster members. |
|   6 | Thinking one query equals one TCP packet            | A MongoDB message such as `OP_MSG` may span multiple TCP segments.                           | MongoDB messages and TCP packets are not 1:1.                  |
___

# Level 3 - Interacting with MongoDB 
## Setting up the Environment
### How to access MongoDB server through Linux
You need:
- A Linux machine (client)
- MongoDB server IP address
- MongoDB port (default: `27017`)
- MongoDB username/password (if authentication is enabled)
- Network access to the server

Example:
```
MongoDB Server IP: 192.168.1.100
Port: 27017
Username: admin
Password: password123
```
#### Step 1 : Check Network Connection
Before installing anything, test if the MongoDB server is reachable.

Ping the server
```
ping 192.168.1.100
```
If you get replies, the machine is reachable.

#### Step 2: Install MongoDB Shell (`mongosh`)
`mongosh` is the official command-line tool used to connect to MongoDB servers.
###### Download Using Terminal
```bash
wget https://downloads.mongodb.com/compass/mongodb-mongosh_2.10.0_amd64.deb
```
###### Install the Downloaded File
```bash
sudo dpkg -i mongodb-mongosh_2.10.0_amd64.deb
```

Now you can run:
```shell
mongosh --version
```
Installation is complete.

#### Step 3: Connect to Remote MongoDB Server
###### Without Authentication
Use:
```bash
mongosh "mongodb://SERVER_IP:27017"
```

Example:
```bash
mongosh "mongodb://192.168.1.100:27017"
```

Successful connection:
```
Connecting to: mongodb://192.168.1.100:27017/
test>
```

###### Connect with Username and Password
Use:
```bash
mongosh "mongodb://USERNAME:PASSWORD@SERVER_IP:27017/DATABASE"
```

Example:
```bash
mongosh "mongodb://admin:password123@192.168.1.100:27017/admin"
```

###### Connect Using Separate Options (Safer)
Instead of putting the password in the command:
```bash
mongosh --host 192.168.1.100 --port 27017 -u admin -p
```

It will ask:
```
Enter password:
```
Then enter your password.

#### Step 4: Verify Connection / Queries
After connecting:
###### Show databases
```bash
show dbs
```

Example:
```
admin
config
test
users
```
###### Select database
```bash
use test
```
###### Show collections
```bash
show collections
```
###### View documents
Example:
```bash
db.users.find()
```

#### Step 5: Exit MongoDB Shell
Type:
```
exit
```
or press:
```
CTRL + D
```
___
## Commonly Used `mongosh` Commands (Top 15)
| Command                   | What it Does            | Example                                                   |
| ------------------------- | ----------------------- | --------------------------------------------------------- |
| `show dbs`                | List databases          | `show dbs`                                                |
| `use dbname`              | Select database         | `use mydatabase`                                          |
| `show collections`        | List tables/collections | `show collections`                                        |
| `db.collection.find()`    | View data               | `db.users.find()`                                         |
| `db.collection.findOne()` | View one record         | `db.users.findOne()`                                      |
| `insertOne()`             | Add data                | `db.users.insertOne({name:"John", age:25})`               |
| `insertMany()`            | Add multiple records    | `db.users.insertMany([{name:"Alice"},{name:"Bob"}])`      |
| `updateOne()`             | Modify data             | `db.users.updateOne({name:"John"},{$set:{age:30}})`       |
| `updateMany()`            | Modify many records     | `db.users.updateMany({role:"user"},{$set:{active:true}})` |
| `deleteOne()`             | Delete record           | `db.users.deleteOne({name:"John"})`                       |
| `deleteMany()`            | Delete records          | `db.users.deleteMany({active:false})`                     |
| `countDocuments()`        | Count records           | `db.users.countDocuments()`                               |
| `sort()`                  | Sort results            | `db.users.find().sort({age:1})`                           |
| `limit()`                 | Limit output            | `db.users.find().limit(5)`                                |
| `exit`                    | Quit shell              | `exit`                                                    |
#### Common Commands
| Command            | Purpose                                              | Example            |
| ------------------ | ---------------------------------------------------- | ------------------ |
| `show dbs`         | List all databases                                   | `show dbs`         |
| `use <database>`   | Switch to a database (creates it when data is added) | `use testdb`       |
| `db`               | Show current database                                | `db`               |
| `show collections` | List collections in current database                 | `show collections` |
| `help`             | Show general help                                    | `help`             |
| `db.help()`        | Show database-related commands                       | `db.help()`        |
| `exit`             | Exit MongoDB shell                                   | `exit`             |
#### Database & Collection Commands
| Command                   | Purpose                 | Example                        |
| ------------------------- | ----------------------- | ------------------------------ |
| `db.createCollection()`   | Create a new collection | `db.createCollection("users")` |
| `db.<collection>.drop()`  | Delete a collection     | `db.users.drop()`              |
| `db.dropDatabase()`       | Delete current database | `db.dropDatabase()`            |
| `db.getCollectionNames()` | List collections        | `db.getCollectionNames()`      |
#### Insert Data
| Command        | Purpose                   | Example                                            |
| -------------- | ------------------------- | -------------------------------------------------- |
| `insertOne()`  | Insert one document       | `db.users.insertOne({name:"John", age:25})`        |
| `insertMany()` | Insert multiple documents | `db.users.insertMany([{name:"Sam"},{name:"Tom"}])` |
Example:
```JSON
db.users.insertOne({
  name: "Alice",
  age: 30,
  role: "admin"
})
```
#### View / Read Data
| Command     | Purpose              | Example                    |
| ----------- | -------------------- | -------------------------- |
| `find()`    | Show all documents   | `db.users.find()`          |
| `findOne()` | Show one document    | `db.users.findOne()`       |
| `find({})`  | Show all documents   | `db.users.find({})`        |
| `pretty()`  | Format output nicely | `db.users.find().pretty()` |
Example:
```JSON
db.users.find({name:"Alice"})
```

Output:
```JSON
{
  name: "Alice",
  age: 30,
  role: "admin"
}
```
#### Query Operators
| Command | Purpose       | Example                          |
| ------- | ------------- | -------------------------------- |
| `$eq`   | Equal         | `{age: {$eq:25}}`                |
| `$gt`   | Greater than  | `{age: {$gt:18}}`                |
| `$gte`  | Greater/equal | `{age: {$gte:18}}`               |
| `$lt`   | Less than     | `{age: {$lt:50}}`                |
| `$lte`  | Less/equal    | `{age: {$lte:50}}`               |
| `$ne`   | Not equal     | `{status: {$ne:"inactive"}}`     |
| `$in`   | Match values  | `{role: {$in:["admin","user"]}}` |
Examples:
```JSON
db.users.find({age: {$gt:25}})
```

```JSON
db.users.find({role:"admin"})
```
#### Update Data
| Command        | Purpose                   | Example                                             |
| -------------- | ------------------------- | --------------------------------------------------- |
| `updateOne()`  | Update one document       | `db.users.updateOne({name:"John"},{$set:{age:30}})` |
| `updateMany()` | Update multiple documents | `db.users.updateMany({},{$set:{active:true}})`      |
| `$set`         | Change field value        | `{$set:{age:40}}`                                   |
| `$unset`       | Remove a field            | `{$unset:{age:""}}`                                 |
| `$inc`         | Increase/decrease number  | `{$inc:{age:1}}`                                    |
Example:
```JSON
db.users.updateOne(
 {name:"Alice"},
 {$set:{role:"manager"}}
)
```
#### Delete Data
| Command        | Purpose                   | Example                               |
| -------------- | ------------------------- | ------------------------------------- |
| `deleteOne()`  | Delete one document       | `db.users.deleteOne({name:"John"})`   |
| `deleteMany()` | Delete multiple documents | `db.users.deleteMany({active:false})` |
Example:
```JSON
db.users.deleteOne({name:"Alice"})
```
#### Useful Admin Commands
| Command             | Purpose                    |
| ------------------- | -------------------------- |
| `db.serverStatus()` | Show MongoDB server status |
| `db.stats()`        | Show database statistics   |
| `db.version()`      | Show MongoDB version       |
| `db.currentOp()`    | Show running operations    |
| `rs.status()`       | Replica set status         |
