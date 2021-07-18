# 2.Web Architecture

## Client-Server Architecture

![Client-Server Architecture]()

**The architecture works on a request-response model. The client sends the request to the server for information and the server responds with it.**


## Client

The user interface runs on the client. The client can be a mobile app, a desktop or a tablet like an iPad. It can also be a web-based console, running commands to interact with the backend server.

![Client]()

## Types of Clients
![Thin Client and Thick Client]()

- Thin client: Thin client is the client that holds just the user interface of the application. It has no business logic of any sort. For every action, the client sends a request to the backend server. Just like in a three-tier application.

- Thick Client: the thick client holds all or some part of the business logic. These are the two-tier applications. If you remember, we’ve already discussed. The typical examples of fat clients are utility apps, online games, etc.

## Server

**The primary task of a web server is to receive the requests from the client and provide the response after executing the business logic based on the request parameters received from the client.**

- application server
- proxy server
- mail server
- file server
- virtual server

## Communication Between the Client and the Server

![REST API Architecture]()

- Request-response model: The client and the server have a request-response model. The client sends the request and the server responds with the data. If there is no request, there is no response.

- HTTP protocol: The entire communication happens over the HTTP protocol. It is the protocol for data exchange over the World Wide Web. HTTP protocol is a request-response protocol that defines how information is transmitted across the web. It’s a *stateless protocol*, and every process over HTTP is executed independently and has no knowledge of previous processes.

- REST API and API Endpoints: Speaking from the context of modern N-tier web applications, every client has to hit a REST endpoint to fetch the data from the backend.

## What is a REST API?

**REST stands for Representational State Transfer. It’s a software architectural style for implementing web services. Web services implemented using the REST architectural style are known as the RESTful Web services.**

- The communication between the client and the server is a stateless process. 
- Every time a client interacts with the backend, the client has to send the authentication information to it as well.
- This entirely decouples the backend and the client code.
- There was no need to worry about the type of the client. Just provide the endpoints and the response will generally contain data in the JSON or any other standard data transport format.

**An API/REST/Backend endpoint means the URL of a service.**

### Application development before the REST API #

Tightly coupled the backend code with the client. Java Server Pages (JSP) is one example of this.


![API Gateway]()


## HTTP Push and Pull (two modes of data transfer between client and server)

### HTTP PULL
The client sends the request and the server responds with the data. This is the default mode of HTTP communication. The client pulls the data from the server whenever required. It keeps doing this over and over to fetch the updated data.

**An important thing to note here is that every request to the server and the response to it consumes bandwidth. Every hit on the server costs the business money and adds more load on the server.**

When there is no updated data available on the server, the client doesn't know that and keeps sending request to the server over and over, which is a waste of resources. *Excessive pulls by the clients have the potential to bring down the server.*

**two ways of pulling/fetching data from the server**

- Sending an HTTP GET request to the server manually by triggering an event by clicking a button or any other element on the web page.
- Fetching data dynamically at regular intervals by using AJAX without any human intervention.

#### AJAX – Asynchronous JavaScript and XML
![AJAX]()

**This dynamic technique of requesting information from the server after regular intervals is known as polling.**

### HTTP PUSH
The client sends the request for particular information to the server just once. After the first request, the server keeps pushing the new updates to the client whenever they are available.

Clients use *Asynchronous JavaScript & XML (AJAX)* to send requests to the server in the HTTP PULL based mechanism.

There are multiple technologies involved in the HTTP PUSH based mechanism such as:

- Ajax Long polling
- Web Sockets
- HTML5 Event Source
- Message Queues
- Streaming over HTTP

#### HTTP PUSH mechanism
- Time To Live (TTL)
    - In HTTP PULL, there is a Time to Live (TTL) for every request. It could be 30 secs to 60 secs. 
    - If the client doesn’t receive a response from the server within the TTL, the browser kills the connection and the client has to re-send the request hoping it receives the data from the server before the TTL ends again.  
    - There is a limit to the number of open connections a server can handle at once. If the connections don’t close and new ones are being introduced, over time, the server will run out of memory.
- Persistent connection
    - A persistent connection is a network connection between the client and the server that remains open for further requests and responses, as opposed to being closed after a single communication.
    ![Persistent connection]()
    - Heartbeat interceptors: These are just blank request responses between the client and the server to prevent the browser from killing the connection.
    - Is Resource Intensive
    - Use cases: a browser-based multiplayer game has a pretty large amount of request-response activity within a certain time compared to a regular web application.

#### HTTP Push-Based Technologies
- Web Sockets
    - A Web Socket connection is preferred when we need a persistent **bi-directional low latency data flow** from the client to server and back.
    - use cases:  messaging, chat applications, real-time social streams, and browser-based massive multiplayer games
    - Protocal: Web Sockets tech doesn’t work over HTTP. It runs over TCP. The server and the client should both support Web Sockets, or else the system won’t work.

- AJAX – Long polling
    -  In this technique instead of immediately returning the response, the server holds the response until it finds an update to be sent to the client.
    - The server doesn’t return an empty response. If the connection breaks, the client has to re-establish the connection to the server. **Dynamically update the web page by polling the server at regular intervals.**
    - Upside: There are a smaller number of requests sent from the client to the server compared to the regular polling mechanism. This reduces a lot of network bandwidth consumption.
    - use cases: simple asynchronous data fetch use cases when you do not want to poll the server every now and then
    - protocal: HTTP

- HTML5 Event-Source API and Server-Sent Events
    - Instead of the client polling for data, the server automatically pushes the data to the client whenever the updates are available. The incoming messages from the server are treated as events.
    - the servers can initiate data transmission towards the client once the client has established the connection with an initial request. This helps get rid of a huge number of blank request-response cycles cutting down the bandwidth consumption by notches.
    - the data flow is in one direction only, that is **from the server to the client**.
    - use cases: SSE is ideal for scenarios like a real-time Twitter feed, displaying stock quotes on the UI, real-time notifications etc.
    - protocal: HTTP

- Streaming over HTTP
     - use cases: we need to **stream large data over HTTP** by breaking it into smaller chunks.
     - The technique is primarily used for streaming multimedia content, like large images, videos etc, over HTTP.
     - protocal: HTTP
     ![REST API VS Streaming API]()

### Client-Side vs. Server-Side Rendering

- Client-side rendering
    - browser receives the response from server, and renders the response on the window in the form of an HTML page.
    - components: Browser engine, Rendering engine, JavaScript interpreter, Networking and the UI backend, Data storage etc.
    - The rendering engine constructs the DOM tree and renders and paints the construction. Naturally, all this activity needs a bit of time.
    - use case: Client-side rendering works best for modern dynamic AJAX-based websites.

- Server-side rendering
    - Generate HTML on the server and directly send the HTML page to the UI to reduce the rendering time.
    - use case: delivering static content but not a good idea for modern dynamic AJAX-based websites since the approach generates the entire page on the server. This process consumes unnecessary bandwidth and also fails to provide a smooth user experience.

- Hybrid approach for AJAX-based websites
    - use server-side rendering for the home page and for the other static content on our website and use client-side rendering for the dynamic pages.