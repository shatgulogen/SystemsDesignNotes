# SystemsDesignNotes

Study notes for systems design fundamentals referring to algoexpert.io and educative.io for the original content.

---

> Click :star:if you think this is helpful for you.

---

### Table of Contents

| No. | Contents                                                        |
| --- | --------------------------------------------------------------- |
|     | **Systems Design Fundamentals**                                 |
| 1   | [Client-Server Model](#client-server-model)                     |
| 2   | [Network Protocols](#network-protocols)                         |
| 3   | [Storage](#storage)                                             |
| 4   | [Latency And Throughput](#latency-and-throughput)               |
| 5   | [Availability](#availability)                                   |
| 6   | [Caching](#caching)                                             |
| 7   | [Proxies](#proxies)                                             |
| 8   | [Load Balancers](#load-balancers)                               |
| 9   | [Hashing](#hashing)                                             |
| 10  | [Relational Databases](#relational-databases)                   |
| 11  | [Key-Value Stores](#key-value-stores)                           |
| 12  | [Specialized Storage Paradigms](#specialized-storage-paradigms) |
| 13  | [Relication And Sharding](#relication-and-sharding)             |
| 14  | [Leader Election](#leader-election)                             |
| 15  | [Peer-To-Peer Network](#peer-to-peer-network)                   |
| 16  | [Polling And Streaming](#polling-and-streaming)                 |
| 17  | [Configuration](#configuration)                                 |
| 18  | [Rate Limiting](#rate-limiting)                                 |
| 19  | [Logging And Monitoring](#logging-and-monitoring)               |
| 20  | [Publish/Subscribe Pattern](#publish-pattern-subscribe-pattern) |
| 21  | [MapReduce](#mapReduce)                                         |
| 22  | [Security And HTTPS](#security-and-https)                       |
| 23  | [API Design](#api-design)                                       |

## Systems Design Fundamentals

---

1. ### Client-Server Model

---

#### **Client**

A machine or process that requests data or service from a server.
Note that a single machine or piece of software can be both a client and a server at the same time. For instance, a single machine could act as a server for end users and as a client for a database.

#### **Server**

A machine or process that provides data or service for a client, usually by listening for incoming network calls.
Note that a single machine or piece of software can be both a client and a server at the same time. For instance, a single machine could act as a server for end users and as a client for a database.

#### **Client--Server Model**

The paradigm by which modern systems are designed, which consists of clients requesting data or service from servers and servers providing data or service to clients.

#### **IP Address**

An address given to each machine connected to the public internet. IPv4 addresses consist of four numbers separated by dots: a.b.c.d where all four numbers are between 0 and 255. Special values include:

-   127.0.0.1: Your own local machine. Also referred to as localhost.
-   192.168.x.y: Your private network. For instance, your machine and all machines on your private network will usually have the 192.168 prefix.

#### **Port**

In order for multiple programs to listen for new network connections on the same machine without colliding, they pick a port to listen on. A port is an integer between 0 and 65,535(2^16 ports total).
Typically, ports 0-1023 are reserved for system ports (also called well-known ports) and shouldn't be used by user-level processes. Certain posts have pre-defined uses, and although you usually won't be required to have them memorized, they can sometimes come in handy:

-   22: Secure Shell
-   53: DNS lookup
-   80: HTTP
-   443: HTTPS

#### **DNS**

Short for **Domain Name System**, it describes the entities and protocols involved in the translation from domain names to IP Addresses. Typically, machines make a DNS query to a well known entity which is responsible for returning the IP address (or multiple ones) of the requested domain name in the response.

**[↑ Back to Top](#table-of-contents)**

---

2. ### Network protocols

---

#### **IP**

Stands for Internet Protocol. This network protocol outlines how almost all machine-to-machine communications should happen in the world. Other protocols like TCP, UDP and HTTP are built on top of IP.

#### **TCP**

Network protocol built on top of the Internet Protocol (IP). Allows for ordered, reliable data delivery between machines over the public internet by creating a connection.

TCP is usually implemented in the kernel, which exposes sockets to applications that they can use to stream data through an open connection.

#### **HTTP**

The HyperText Transfer Protocol is a very common network protocol implemented on top of TCP. Clients make HTTP requests, and servers respond with a response.

Requests typically have the following schema:

    ```
    host: string ( example: algoexpert.io)
    port: integer ( example: 80 or 443)
    method: string ( example: GET, PUT, POST, DELETE, OPTIONS or PATCH)
    headers: pair list ( example: "Content- Type" => "application/json")
    body: opaque sequence of bytes

    ```

Responses typically have the following schema:

    ```
    status code: integer ( example: 200, 401)
    headers: pair list ( example: "Content-Length" => 1238)
    body: opaque sequence of bytes

    ```

#### **IP Packet**

Sometimes more broadly referred to as just a (network) packet, an IP Packet is the smallest unit used to describe data being sent over IP, aside from bytes. An IP packet consists of:

-   an IP header, which contains the source and destination IP addresses as well as other information related to the network
-   a payload, which is just the data being sent over the network

**[↑ Back to Top](#table-of-contents)**

---

3. ### Storage

---

#### **Databases**

Databases are programs that either use disk or memory to do 2 core things: **record data** and **query data**. In general, they are themselves servers that are long lived and interact with the rest of your application through network calls, with protocols on top of TCP or even HTTP.

Some databases only keep records in memory, and the users of such databases know those records may be lost forever if the machine or process dies.

For the most part though, databases need persistence of those records, and this cannot use memory. This means that you have to write your data to disk. Anything written to disk will remain through power loss or network partitions, so that's what is used to keep permanent records.

Since machines die often in a large scale system, special disk partitions or volumes are used by the database processes, and those volumes can get recovered even if the machine were to go down permanently.

#### **Disk**

Usually refers to either **HDD (hard-disk drive)** or **SSD (solid-state drive)**. Data written to disk will persist through power failures and general machine crashes.
Disk is also referred to as non-volatile storage.

SSD is far faster than HDD (see latencies of accessing data from SSD and HDD) but also far more expensive from a financial point of view.

-   Because of that, HDD will typically be used for data that's rarely accessed or updated, but that's stored for a long time.
-   SSD will be used for data that's frequently accessed and updated.

#### **Memory**

Short for **Random Access Memory (RAM)**.
Data stored in memory will be lost when the process that has written that data dies.

#### **Persistent Storage**

Usually refers to disk, but in general it is any form of storage that persists if the process in charge of managing it dies.

**[↑ Back to Top](#table-of-contents)**

---

4. ### Latency And Throughput

---

#### **Latency**

The time it takes for a certain operation to complete in a system. Most often this measure is a time duration, like milliseconds or seconds.
Orders of magnitude:

-   Reading 1 MB from RAM: 0.25ms
-   Reading 1 MB from SSD: 1ms
-   Transfer 1 MB over Network: 10ms
-   Reading 1 MB from HDD: 20ms
-   Inter-Continental Round Trip: 150ms

#### **Throughput**

The number of operations that a system can handle properly per time unit. For instance the throughput of a server can often be measured in requests per second(RPS or QPS).

**[↑ Back to Top](#table-of-contents)**

---

5. ### Availability

---

#### **Process**

A program that is currently running on a machine. Always assume that any process may get terminated at any time in a sufficiently large system.

#### **Node/Instance/Host**

These three terms refer to the same thing most of the time: a virtual or physical machine on which the developer runs processes. Sometimes the word server also refers to this same concept.

#### **Availability**

The odds of a particular server or service being up and running at any point in time, usually measured in percentages. A server that has 99% availability will be operational 99% of the time( this would be described as having two nines of availability).

#### **High Availability**

Used to describe systems that have particularly high levels of availability, typically 5 nines or more; sometimes abbreviated "HA".

#### **Nines**

Percentages of uptime. For example, 5 nines of availability means an uptime of 99.999% of the time. Below are the downtimes expected per year depending on those 9s:

-   99% (two 9s): 87.7 hours
-   99.9% (three 9s): 8.8 hours
-   99.99%: 52.6 minutes
-   99.999%: 5.3 minutes

#### **Redundancy**

The process of replicating parts of a system in an effort to make it more reliable.

#### **SLA**

Short for "service-level agreement", an SLA is a collection of guarantees given to a customer by a service provider. SLAs typically make guarantees on a system's availability, amongst other things. SLAs are made up of one or multiple SLOs.

#### **SLO**

Short for "service-level objective", an SLO is a guarantee given to a customer by a service provider. SLOs typically make guarantees on a system's availability, amongst other things. SLOs constitute an SLA.

**[↑ Back to Top](#table-of-contents)**

---

6. ### Caching

---

#### **Cache**

A piece of hardware or software that stores data, typically meant to retrieve that data faster than otherwise.

Caches are often used to store responses to network requests as well as results of computationally-long operations.

Note that data in a cache can become **stale** if the main source of truth for that data(i.e., the main database behine the cache) gets updated and the cache doesn't.

#### **Cache Hit**

When requested data is found in a cache.

#### **Cache Miss**

When requested data could have been found in a cache but isn't. This is used to refer to a negative consequence of a system failure or of a poor design choice. For example:

If a server goes down, our load balancer will have to forward requests to a new server, which will result in cache miss.

#### **Cache Eviction Policy**

The policy by which values get evicted or removed from a cache. Popular cache eviction policies include **LRU**(least-recently used), **FIFO**(first in first out), and **LFU**(least-frequently used).

#### **Cache Delivery Network**

A **CDN** is a third-party service that acts like a cache for your servers. Sometimes, web applications can be slow for users in a particular region if your servers are located only in another region. A CDN has servers all sround the world, meaning that the latency to a CDN's servers will almost always be far better than the latency to your servers. A CDN's servers are often referred to as **PoPs**(Points of Presence). Two of the most popular CDNs are **Cloudflare** and **Google Cloud CDN**.

**[↑ Back to Top](#table-of-contents)**

---

7. ### Proxies

---

#### **Forward Proxy**

A server that sits between a client and servers and acts on behalf of the client, typically used to mask the client's identity (IP address). Note that forwards proxies are often referred to as just proxies.

#### **Reverse Proxy**

A server that sits between a client and servers and acts on behalf of the servers, typically used for logging, load balancing, or caching.

#### **Nginx**

Pronounced "engine X", Nginx is a very popularwebserver that's often used as a **reverse proxy** and **load balancer**.

**[↑ Back to Top](#table-of-contents)**

---

8. ### Load Balancers

---

#### **Load Balancer**

A type of reverse proxy that distributes traffic across servers. Load balancers can be found in many parts of the system, from the DNS layer all the way to the database layer.

#### **Server-Selection Strategy**

How a load balancer chooses servers when distributing traffic amongst multiple servers. Commonly used strategies include round-robin, random selection, performance-based selection(choosing the server with the best performance metrics, like the fastest response time or the least amount of traffic), and IP-based routing.

#### **Hot Spot**

When distributing a workload across a set of servers, that workload might be spread unevenly. This can happen if your **sharding key** or your **hashing function** are suboptimal, or if your workload is natually skewed: some servers will receive a lot more traffic than others, thus creating a "hot spot".

**[↑ Back to Top](#table-of-contents)**

---

9. ### Hashing

---

#### **Hashing Function**

A function that takes in a specific data type(such as a string or an identifier) and outputs a number. Different inputs may have the same output, but a good hashing function attemps to minimize those hashing collisions(which is equivalent to maximizing **uniformity**).

#### **Consistent Hashing**

A type of hashing that minimizes the number of keys that need to be remapped when a hash table gets resized. It's often used by load balancers to distribute traffic to servers; it minimizes the number of requests that get forwarded to different servers when new servers are added or when existing servers are brought down.

#### **Rendezvous Hashing**

A type of hashing also called **highest random weight** hashing. Allows for minimal re-distribution of mappings when a server goes down.

#### **SHA**

Short for "Secure Hash Algorithms", the SHA is a collection of cryptographic hash functions used in the industry. These days, SHA-3 is a popular choice to use in a system.

**[↑ Back to Top](#table-of-contents)**

---

10. ### Relational Databases

---

#### **Relational Database**

A type of structured database in which data is stored following a tabular format; often supports powerful querying using SQL.

#### **Non-Relational Database**

In contrast with relational databases (SQL databases), a type of database that is free of imposed, tabular-like structure. Non-relational databases are often referred as NoSQL databases.

#### **SQL**

Structured Query Language. Relational databases can be used using a derivative of SQL such as PostgresSQL in the case of Postgres.

#### **SQL Database**

Any database that supports SQL. This term is often used synonymously with "Relational Database", though in practice, not every relational database supports SQL.

#### **NoSQL Database**

Any database that is not SQL-compatible is called NoSQL.

#### **ACID Transaction**

A type of database transaction that has four important properties:

-   **Atomicity**: The operations that constitute the transaction will either all succeed or all fail. There is no in-between state.
-   **Consistency**: The transaction cannot bring the database to an invalid state. After the transaction is committed or rolled back, the rules for each record will still apply, and all future transactions will see the effect of the transaction. Also named **Strong Consistency**.
-   **Isolation**: The execution of multiple transactions concurrently will have the same effect as if they had been executed sequentially.
-   **Durability**: Any committed transaction is written to non-volatile storage. It will not be undone by a crash, power loss, or network partition.

#### **Database Index**

A special auxiliary data structure that allows your database to perform certain queries much faster. Indexes can typically only exist to reference structured data, like data stored in relational databases. In practice, you create an index on one or multiple columns in your database to greatly speed up **read** queries that you run very often, with the downside of slightly longer **writes** to your database, since writes have to also take place in the relevant index.

#### **Strong Consistency**

Strong Consistency refers to the consistency of ACID transactions, as opposed to **Eventual Consistency**.

#### **Eventual Consistency**

A consistency model which is unlike **Strong Consistency**. In this model, reads might return a view of the system that is stale. An eventually consistent datastore will give guarantees that the state of the database will eventually reflect writes within a time period (could be 10 seconds, or minutes).

#### **Postgres**

A relational database that uses a dialect of SQL called PostgreSQL/ Provides ACID transactions.

**[↑ Back to Top](#table-of-contents)**

---

10. ### Key-Value Stores

---

#### **Key-Value Store**

A Key-Value Store is a flexible NoSQL database that's often used for caching and dynamic configuration. Popular options include DynamoDB, Etcd, Redis, and ZooKeeper.

#### **Etcd**

Etcd is a strongly consistent and highly available key-value store that's often used to implement leader election in a system.

#### **Redis**

An in-memory key-value store. Does offer some persistent storage options but is typically used as a really fast, best-effort caching solution. Redis is also often used to implement **rate limiting**.

#### **ZooKeeper**

ZooKeeper is a strongly consistent, highly available key-value store. It's often used to store important configuration or to perform leader election.

**[↑ Back to Top](#table-of-contents)**

---

11. ### Specialized Storage Paradigms

---

#### **Blob Storage**

#### **Time Series Database**

#### **Graph Database**

#### **Cypher**

#### **Spatial Database**

#### **Quadtree**

#### **Google Cloud Storage**

#### **S3**

#### **InfluxDB**

#### **Prometheus**

#### **Neo4j**

**[↑ Back to Top](#table-of-contents)**

---

12. ### Replication And Sharding

---

#### **Replication**

#### **Sharding**

#### **Hot Spot**

When distributing a workload across a set of servers, that workload might be spread unevenly. This can happen if your **sharding key** or your **hashing function** are suboptimal, or if your workload is natually skewed: some servers will receive a lot more traffic than others, thus creating a "hot spot".

**[↑ Back to Top](#table-of-contents)**
