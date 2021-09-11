# Grokking System Design Interview Notes-System Design Problems

## A step by step guide
### Step 1: Requirements clarifications
**ask questions about the exact scope of the problem we are trying to solve**

Twitter Example

- Will users of our service be able to post tweets and follow other people?
- Should we also design to create and display the user’s timeline?
- Will tweets contain photos and videos?
- Are we focusing on the backend only, or are we developing the front-end too?
- Will users be able to search tweets?
- Do we need to display hot trending topics?
- Will there be any push notification for new (or important) tweets?

### Step 2: Back-of-the-envelope estimation
**Estimate the scale of the system we’re going to design. This will also help later when we focus on scaling, partitioning, 
load balancing, and caching.**
- What scale is expected from the system (e.g., number of new tweets, number of tweet views, number of timeline generations per sec., etc.)?
- How much storage will we need? We will have different storage requirements if users can have photos and videos in their tweets.
- What network bandwidth usage are we expecting? This will be crucial in deciding how we will manage traffic and balance load between servers.

### Step 3: System interface definition
**Define what APIs are expected from the system.**

```
postTweet(user_id, tweet_data, tweet_location, user_location, timestamp, …)  
```
```
generateTimeline(user_id, current_time, user_location, …) 
```
```
markTweetFavorite(user_id, tweet_id, timestamp, …)
```

### Step 4: Defining data model
**Defining the data model in the early part of the interview will clarify how data will flow between different system components. 
Later, it will guide for data partitioning and management. The candidate should identify various system entities, 
how they will interact with each other, and different aspects of data management like storage, transportation, encryption, etc. **

**User:** UserID, Name, Email, DoB, CreationDate, LastLogin, etc.
**Tweet:** TweetID, Content, TweetLocation, NumberOfLikes, TimeStamp, etc.
**UserFollow:** UserID1, UserID2
**FavoriteTweets:** UserID, TweetID, TimeStamp

**Which database system should we use? Will NoSQL like Cassandra best fit our needs, or should we use a MySQL-like solution? 
What kind of block storage should we use to store photos and videos?**

### Step 5: High-level design
**Draw a block diagram with 5-6 boxes representing the core components of our system**

### Step 6: Detailed design
**Dig deeper into two or three major components; We should present different approaches, their pros and cons, and explain why we will
prefer one approach over the other. the only important thing is to consider tradeoffs between different options while keeping system 
constraints in mind.**

Twitter Example
- Since we will be storing a massive amount of data, how should we partition our data to distribute it to multiple databases? Should we try to store all the data of a user on the same database? What issue could it cause?
How will we handle hot users who tweet a lot or follow lots of people?
Since users’ timeline will contain the most recent (and relevant) tweets, should we try to store our data so that it is optimized for scanning the latest tweets?
How much and at which layer should we introduce cache to speed things up?
What components need better load balancing?
- How will we handle hot users who tweet a lot or follow lots of people?
- Since users’ timeline will contain the most recent (and relevant) tweets, should we try to store our data so that it is optimized for scanning the latest tweets?
- How much and at which layer should we introduce cache to speed things up?
- What components need better load balancing?

### Step 7: Identifying and resolving bottlenecks
**Try to discuss as many bottlenecks as possible and different approaches to mitigate them.**

- Is there any single point of failure in our system? What are we doing to mitigate it?
- Do we have enough replicas of the data so that we can still serve our users if we lose a few servers?
- Similarly, do we have enough copies of different services running such that a few failures will not cause a total system shutdown?
- How are we monitoring the performance of our service? Do we get alerts whenever critical components fail or their performance degrades?
