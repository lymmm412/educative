# 3.Scalability

## What is scalability?
**Defination: Scalability means the ability of the application to handle and withstand increased workload without sacrificing the latency.**
![scalable application]()

## What is latency
**Defination: Latency is the amount of time a system takes to respond to a user request.**\\

**Minimum latency is what efficient software systems strive for. No matter how much the traffic load on a system builds up, the latency should not go up. This is what scalability is.**

### measuring latency
![latency]()
- Network latency: Network latency is the amount of time that the network takes to send a data packet from point A to point B. The network should be efficient enough to handle the increased traffic load on the website. **To cut down the network latency, businesses use CDN and try to deploy their servers across the globe as close to the end-user as possible.**

- Application latency: Application latency is the amount of time the application takes to process a user request. There are more than a few ways to cut down the application latency. **The first step is to run stress and load tests on the application and scan for the bottlenecks that slow down the system as a whole. We’ll talk more about it in the upcoming lesson.**

## Types of Scalability
- vertical scaling: Vertical scaling means adding more power to your server. Vertical scaling is also called scaling up. In this type of scaling we increase the power of the hardware running the app. **This is the simplest way to scale because it doesn’t require any code refactoring or making any complex configurations and stuff.**
    ![Vertical scaling]()
    - pros: 1)Tech: **Simpler** in comparison to scaling horizontally because we do not have to touch the code or make any complex distributed system configurations. 2)Cost: It takes much less administrative, monitoring, and management efforts as opposed to managing a distributed environment.
    - cons: **low availability**. The servers are powerful but few in number. There is always a risk of them going down and the entire website going offline,
    -use case: A single server is enough to manage the traffic, so go ahead with vertical scaling when you know that the traffic load will not increase significantly.

- horizontal scaling: Horizontal scaling, also known as scaling out, means adding more hardware to the existing hardware resource pool. This increases the computational power of the system as a whole. Horizontal scaling also provides us with the ability to dynamically scale in real-time as the traffic on our website increases and decreases over a period of time as opposed to vertical scaling which requires pre-planning and a stipulated time to be pulled off.
    ![horizontal scaling]()
    - code: If you need to run the code in a distributed environment, it needs to be stateless. There should be no state in the code. **There can be no static instances in the class. Rather, use a persistent memory like a Key-value store to hold the data and remove all the state/static variable from the class.** This is why functional programming became so popular with distributed systems. The functions don’t retain any state.
    ![microservice architecture]()
    - pros: no limit to augmenting the hardware capacity. Data is replicated across different geographical regions as nodes and data centers are set up across the globe.
    - use case: If your app is a public-facing social app like a social network, a fitness app, or something similar, then traffic is expected to spike exponentially in the near future. Both high availability and horizontal scalability is important to you. Build to deploy it on the cloud, and always have horizontal scalability in mind right from the start.

- Cloud elasticity: The biggest reason why cloud computing become so popular in the industry is the ability to scale up and down dynamically. The ability to use and pay only for the resources required by the website became a trend for obvious reasons. If the site has a heavy traffic influx more server nodes get added and when it doesn’t, the dynamically added nodes are removed.

## Primary Bottlenecks That Hurt the Scalability
- database: The server nodes work well, handle millions of requests at a point in time efficiently, yet, the response time of these requests and the latency of the application is very high due to the presence of a single database. There is only so much it can handle. **Just like workload scalability, the database needs to be scaled well. Make wise use of database partitioning, sharding, and multiple database servers to make the module efficient.**

- Application architecture: **A common architectural mistake is not using asynchronous processes and modules whereever required rather all the processes are scheduled sequentially. These tasks should be forwarded to a messaging server as opposed to doing it all sequentially and making the user wait for everything.**

- Not using caching in the application wisely (high availability): Caching can be deployed at several layers of the application and it speeds up the response time by notches. It intercepts all the requests going to the database, reducing the overall load.

- Inefficient configuration and setup of load balancers: Load balancers are the gateway to our application. Using too many or too few of them impacts the latency of our application.

- Adding business logic to the database: The database is just not the place to put business logic. Not only does the whole application tightly coupled it puts an unnecessary load on it.

- Not picking the right database: Need transactions and strong consistency? Pick a relational database. If you can do without strong consistency rather than need horizontal scalability on the fly pick a NoSQL database.

- At the code level: 
    - Using unnecessary loops or nested loops
    - Writing tightly coupled code
    - Not paying attention to the Big-O complexity while writing the code. (be ready to do a lot of firefighting in production)

## How to Improve and Test the Scalability of our Application?

### Tuning the performance of the application – Enabling it to scale better 
- Profiling: Profile the hell out of your app. Run application profiler and code profiler. See which processes are taking too long and which are eating up too many resources. Find out the bottlenecks. Get rid of them.
- Caching: Cache wisely, and cache everywhere. Cache all the static content. Hit the database only when it is really required. Try to serve all the read requests from the cache. Use a write-through cache.
- CDN: Use a Content Delivery Network (CDN). Using a CDN further reduces the latency of the application due to the proximity of the data from the requesting user.
- Data compression: Compress data. Use apt compression algorithms to compress data, and store data in the compressed form. As compressed data consumes less bandwidth, consequently, the download speed of the data on the client will be faster.
- Avoid unnecessary client server requests: Avoid unnecessary round trips between the client and server. Try to club multiple requests into one.

### Testing the scalability of our application
During the scalability testing, different system parameters are taken into account such as the **CPU usage, network bandwidth consumption, throughput, number of requests processed within a stipulated time, latency, memory usage of the program, end-user experience when the system is under heavy load etc.**

In this testing phase, simulated traffic is routed to the system, to study how the system behaves under the heavy load, and how the application scales under the heavy load. Contingencies are planned for unforeseen situations.

As per the anticipated traffic, the appropriate hardware and computational power are provisioned to handle the traffic smoothly with some buffer.

Several load and stress tests are run on the application. Tools like JMeter are pretty popular for running concurrent user tests on the application if you are working on a Java ecosystem. There are a lot of cloud-based testing tools available that help us simulate test scenarios just with a few mouse clicks.
