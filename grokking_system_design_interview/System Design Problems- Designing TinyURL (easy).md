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
