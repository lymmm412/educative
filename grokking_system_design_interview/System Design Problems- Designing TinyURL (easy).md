# Designing a URL Shortening service like TinyURL [easy]
This service will provide short aliases redirecting to long URLs. Similar services: bit.ly, ow.ly, short.io

## 1. Why do we need URL shortening?
**We call these shortened aliases “short links.” Users are redirected to the original URL when they hit these short links. Short links save a lot of space when displayed, printed, messaged, or tweeted. 
Additionally, users are less likely to mistype shorter URLs.**

## 2. Requirements and Goals of the System
- **Functional Requirements:**
  - Given a URL, our service should generate a shorter and unique alias of it. This is called a short link. This link should be short enough to be easily copied and pasted into applications.
  - When users access a short link, our service should redirect them to the original link.
  - Users should optionally be able to pick a custom short link for their URL.
  - Links will expire after a standard default timespan. Users should be able to specify the expiration time.

- **Non-Functional Requirements: (performance)**
  - The system should be highly available. This is required because, if our service is down, all the URL redirections will start failing.
  - URL redirection should happen in real-time with minimal latency
  - Shortened links should not be guessable (not predictable).
 
 - **Extended Requirements:**
    - Analytics; e.g., how many times a redirection happened?
    - Our service should also be accessible through REST APIs by other services.

## 3. Capacity Estimation and Constraints
Our system will be read-heavy. There will be lots of redirection requests compared to new URL shortenings. 
Let’s assume a 100:1 ratio between read and write.

- **Traffic Estimate:**
  Assuming, we will have 500M new URL shortenings per month(write), with 100:1 read/write ratio: ```100*500M=50B``` reads.

  QPS: ```write: 500M / (30*24*3600) =~ 200 URLs/s```, ```read: 100*200 = 20K/s```

- **Storage Estimate:**
  Let’s assume we store every URL shortening request (and associated shortened link) for 5 years. 

  Since we expect to have 500M new URLs every month, the total number of objects we expect to store will be 30 billion: ```500M * 5years * 12 months = 30B```

  assume that each stored object will be approximately 500 bytes. We will need 15TB of total storage: ```30B * 500bytes = 15TB```

- **Bandwidth estimates:**
  ```write: 200URLs/s * 500bytes = 100KB/s```

  ```read: 20K URLs/s * 500bytes = ~10MB/s```

- **Memory estimates:** If we want to cache some of the hot URLs that are frequently accessed, how much memory will we need to store them? 
  If we follow the ***80-20 rule***, meaning 20% of URLs generate 80% of traffic, we would like to cache these ***20% hot URLs.***

  ```Daily read requests(URLs): 20K * 3600sec *24Hours = 1.7B/day```

  ```daily cache memory: 20% * 1.7B * 500bytes = ~170G```

  **since there will be many duplicate requests (of the same URL), our actual memory usage will be less than 170GB.**
  
- **High-level estimates**
  | term | estimate|
  | --- | --- |
  | New URLs(write) | 200/s |
  | URL redirections(read) | 20K/s |
  | Incoming data (write size) | 100KB/s |
  | Outgoing data(read size) | 10MB/s |
  | Storage for 5 years | 15TB |
  | Memory for cache | 170GB |

 ## 4. System APIs
**How do we detect and prevent abuse?** A malicious user can put us out of business by consuming all URL keys in the current design. To prevent abuse, we can limit users via their api_dev_key. Each api_dev_key can be limited to a certain number of URL creations and redirections per some time period (which may be set to a different duration per developer key).

## 5. Database Design
A few observations about the nature of the data we will store:
- We need to store billions of records.
- Each object we store is small (less than 1K).
- There are no relationships between records—other than storing which user created a URL.
- Our service is read-heavy.
We would need two tables: one for storing information about the URL mappings and one for the user’s data who created the short link.

**What kind of database should we use?** Since we anticipate storing billions of rows, and we don’t need to use relationships between objects – a NoSQL store like DynamoDB, Cassandra or Riak is a better choice. A NoSQL choice would also be easier to scale. Please see SQL vs NoSQL for more details.

## 6. Basic System Design and Algorithm
###  Encoding actual URL
- compute a unique hash(MD5 or SHA256) of the given URL
- encode the hash for display(Base36[a-z,0-9], Base62[A-Z, a-z, 0-9], Base64[A-Z, a-z, 0-9,+,/])
- choose the length of the short key: use base64, a 6 letters long key would result in 64^6 = ~68.7 billion possible strings.With 68.7B unique strings, let’s assume six letter keys would suffice for our system.

MD5 hashing will produce a 128-bit hash value. After base64 encoding, we’ll get a string having more than 21 characters (since each base64 character encodes 6 bits of the hash value). Now we only have space for 6 (or 8) characters per short key; Now we only have space for 6 (or 8) characters per short key; how will we choose our key then? **We can take the first 6 (or 8) letters for the key. This could result in key duplication;  we can choose some other characters out of the encoding string or swap some characters.**

**issues:**
- If multiple users enter the same URL, they can get the same shortened URL, which is not acceptable.
- What if parts of the URL are URL-encoded? e.g., http://www.educative.io/distributed.php?id=design, and http://www.educative.io/distributed.php%3Fid%3Ddesign are identical except for the URL encoding.

**Workaround for the issues**
-  We can append an increasing sequence number to each input URL to make it unique and then generate its hash. But can overflow and mpact the performance of the service.
-  append the user id (which should be unique) to the input URL. However, if the user has not signed in, we would have to ask the user to choose a uniqueness key. Even after this, if we have a conflict, we have to keep generating a key until we get a unique one

### Generating keys offline
We can have a standalone Key Generation Service (KGS) that generates random six-letter strings beforehand and stores them in a database (let’s call it key-DB).
**Not only are we not encoding the URL, but we won’t have to worry about duplications or collisions.**

## 7. Data Partitioning and Replication
- **Range Based Partitioning:** We can store URLs in separate partitions based on the hash key’s first letter. **The main problem with this approach is that it can lead to unbalanced DB servers.**
- **Hash-Based Partitioning:**  In this scheme, we take a hash of the object we are storing. We then calculate which partition to use based upon the hash. In our case, we can take the hash of the ‘key’ or the short link to determine the partition in which we store the data object. Our hashing function will randomly distribute URLs into different partitions. **This approach can still lead to overloaded partitions, which can be solved using Consistent Hashing.**

## 8.Cache
We can cache URLs that are frequently accessed.

- **How much cache memory should we have?** We can start with 20% of daily traffic and, based on clients’ usage patterns, we can adjust how many cache servers we need.
- **Which cache eviction policy would best fit our needs?** Least Recently Used can be a reasonable policy for our system. **To further increase the efficiency, we can replicate our caching servers to distribute the load between them.**
- **How can each cache replica be updated?** 

## 9.Load Balancer (LB)
We can add a Load balancing layer at three places in our system:
- Between Clients and Application servers
- Between Application Servers and database servers
- Between Application Servers and Cache servers

**Algorithm: Round Robin** 1. simple to implement. 2. does not introduce any overhead 3.if a server is dead, LB will take it out of the rotation and stop sending any traffic to it. Problem: do not consider the server load, if a server is overloaded or slow, the LB will not stop sending new requests to that server -> **periodically queries the backend server about its load and adjusts traffic based on that.**

**10. Purging or DB cleanup the expired links**
If we chose to continuously search for expired links to remove them, it would put a lot of pressure on our database.

**Lazy cleanup:**
- Whenever a user tries to access an expired link, we can delete the link and return an error to the user.
- A separate Cleanup service can run periodically to remove expired links from our storage and cache. This service should be very lightweight and scheduled to run only when the user traffic is expected to be low.
- We can have a default expiration time for each link (e.g., two years).
- After removing an expired link, we can put the key back in the key-DB to be reused.
- Should we remove links that haven’t been visited in some length of time, say six months? This could be tricky. Since storage is getting cheap, we can decide to keep links forever.

## 11. Telemetry
Some statistics worth tracking: country of the visitor, date and time of access, web page that referred the click, browser, or platform from where the page was accessed.

## 12. Security and Permissions
We can store the permission level (public/private) with each URL in the database. We can also create a separate table to store UserIDs that have permission to see a specific URL. If a user does not have permission and tries to access a URL, we can send an error (HTTP 401) back
