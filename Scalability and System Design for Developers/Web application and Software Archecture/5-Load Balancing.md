# 5. Load Balancing
- services scale well with an increase in traffic load
- services stay highly available.
- Load balancers act as a single point of contact for all the client requests.
![load balancing]()
![load balancing at different components of the application]()

## Performing health checks of the servers with load balancers
To ensure that the user request is always routed to the machine that is up and running, load balancers regularly perform health checks on the machines in the cluster.
![health checks by load balancer]()

## Understanding DNS 
**Domain name system commonly known as DNS is a system that averts the need to remember long IP addresses to visit a website by mapping easy to remember domain names to IP addresses.**

### How does a domain name system work
are four key components
- DNS Recursive nameserver aka DNS Resolver
- Root nameserver
- Top-Level Domain nameserver
- Authoritative nameserver
![domain name system]()

1. browser sends a request to the DNS Recursive nameserver

2. The role of DNS Resolver is to receive the client request and forward it to the Root nameserver to get the address of the Top-Level domain nameserver. As an example, the top-level domain for amazon.com is .com.

3. The DNS Recursive nameserver is generally managed by our ISP Internet service provider. The whole DNS system is a distributed system setup in large data centers managed by internet service providers. These data centers contain clusters of servers that are optimized to process DNS queries in minimal time that is in milliseconds.

4. Once the DNS Resolver receives the address of the top-level domain nameserver, it sends a request to it to fetch the details of the domain name. Top Level domain nameservers hold the data for domains using their top-level domains. For instance, *.com top-level domain nameserve*r will contain information on domains using *.com*. Similarly, a *.edu* Top-Level domain nameserver will hold information on domains using *.edu*.

5. amazon.com domain nameserver is the last server in the DNS query lookup process. The nameserver is responsible for amazon.com domain and is also known as the Authoritative nameserver. This nameserver is owned by the owner of the domain name.

6. Then, DNS Resolver fires a query to the Authoritative nameserver, and it returns the IP address of amazon.com website to the DNS Resolver. DNS Resolver caches the data and forwards it to the client.

7. On receiving the response, the browser sends a request to amazon.com website’s IP address to fetch data from their servers.
![dns working process]()

### DNS load balancing
To spread the user traffic across different clusters in different data centers. There are different ways to set up load balancing. In this lesson, we will discuss DNS load balancing, which is set up at the DNS level on the authoritative server.

With every request, the authoritative server changes the order of the IP addresses in the list in a *round-robin* fashion.

Also, when the client hits an IP, it may not necessarily hit an application server. Instead, it may hit another load balancer implemented at the data center level that manages the clusters of application servers.

### Limitations of DNS load balancing
- It does not take into account the existing load on the servers, the content they hold, their request processing time, their in-service status, and so on.
- since these IP addresses are cached by the client’s machine and the DNS Resolver, there is always a possibility of a request being routed to a machine that is out of service.

**recommend read** [round robin DNS](https://en.wikipedia.org/wiki/Round-robin_DNS)

## Load Balancing Methods
There are primarily three modes of load balancing:
- DNS Load Balancing
- Hardware-based Load Balancing
- Software-based Load Balancing

### Hardware load balancers
Hardware load balancers are highly performant physical hardware. They sit in front of the application servers and distribute the load based on the number of existing open connections to a server, compute utilization, and several other parameters. **They are expensive to set up in comparison to software load balancers, and their upkeep may require a certain skill set. Hardware load balancers are primarily picked because of their top-notch performance.**

### Software load balancers
Software load balancers are pretty advanced compared to DNS load balancing as they consider many parameters such as *content hosted by the servers, cookies, HTTP headers, CPU and memory utilization, load on the network*, and so on to route traffic across the servers. **They also continually perform health checks on the servers to keep an updated list of in-service machines.**

[HAProxy](https://www.haproxy.com/) is one example of a software load balancer that is used widely by the big guns in the industry to scale their systems

### Algorithms/Traffic Routing Approaches Leveraged by Load Balancers
- **Round Robin and Weighted Round Robin**: Sends IP addresses of machines sequentially to the clients. Parameters on the servers, such as load, their CPU consumption, and so on, are not taken into account when sending the IP addresses to the clients.
- **Weighted Round Robin**: based on the server’s compute and traffic handling capacity weights are assigned to them. And then, based on server weights, traffic is routed to them using the Round Robin algorithm.
- **Least connections**: 1)In the first, it is assumed that all the requests will consume an equal amount of server resources, and the traffic is routed to the machine with the least open connections based on this assumption. 2) Machines with the shortest request processing time, smallest CPU utilization, and the least open connections are the right candidates to process future client requests. The least connections approach comes in handy when the server has long opened connections. For instance, consider persistent connections in a gaming application.
- **random**: Following this approach, the traffic is randomly routed to the servers. The load balancer may also find similar servers in terms of existing load, request processing time, and so on.
- **hash**: In this approach, the source IP where the request is coming from and the request URL are hashed to route the traffic to the backend servers. 
    - Hashing the source IP ensures that the request of a client with a certain IP will always be routed to the same server. This facilitates a better user experience as the server has already processed the initial client requests and holds the client’s data in its local memory. There is no need for it to fetch the client session data from the session memory of the cluster and process the request. This reduces latency. Hashing the client IP also enables the client to re-establish the connection with the same server, that was processing its request in case the connection drops.
    - Hashing a URL ensures that the request with that URL always hits a certain cache that already has data on it. This is to ensure that there is no cache miss. This also averts the need for duplicating data in every cache and is, thus, a more efficient way to implement caching.