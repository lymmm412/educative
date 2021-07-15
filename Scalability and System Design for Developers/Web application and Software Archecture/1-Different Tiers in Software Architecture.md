# 1.Different Tiers in Software Architecture

![different components in web service](https://github.com/lymmm412/educative/blob/main/Scalability%20and%20System%20Design%20for%20Developers/Web%20application%20and%20Software%20Archecture/img/different-components-in-web-service.jpg)

## Single Tier Application

**Defination：A single-tier application is an application where the user interface, backend business logic, and the database all reside in the same machine.**

**Advantage**
- No network latency. Every component is located on the same machine. This adds up to the performance of the software.
- Data is easily and quickly available since it is located in the same machine.
- Data safety is at the highest level. User’s data stays in their machine and doesn’t need to be transmitted over a network

**Disadvantage**
- Business has no control over the application. Once the software is shipped, no code or feature changes can be done until the customer manually updates it by connecting to the remote server or by downloading and installing a patch.
- Code in single-tier applications is also vulnerable to being tweaked and reversed engineered.
- Applications’ performance and the look and feel can become inconsistent as the app largely depends on the configuration of the user’s machine.

## Two Tier Application

**Defination: A two-tier application involves a client and a server. The client contains the user interface and the business logic in one machine. Meanwhile, the backend server includes the database running on a different machine. The database server is hosted by the business and has control over it.**

![two tier](https://github.com/lymmm412/educative/blob/main/Scalability%20and%20System%20Design%20for%20Developers/Web%20application%20and%20Software%20Archecture/img/two-tier.jpg)

**The need for two-tier applications**
- The code and the user interface reside in the same machine, there are fewer network calls to the backend server which keeps the latency of the application low.
- Fewer server calls mean less money needs to be spent on the servers which is economical.

## Three-Tier Applications (most popular one)
**Defination: In a three-tier application, the user interface, application logic, and the database all lie on different machines and, thus, have different tiers. They are physically separated.**

![three tier](https://github.com/lymmm412/educative/blob/main/Scalability%20and%20System%20Design%20for%20Developers/Web%20application%20and%20Software%20Archecture/img/three-tier.jpg)

## N-Tier Applications
**Defination: An n-tier application is an application that has more than three components involved.**

**Components**
- Cache
- Message queues for asynchronous behavior
- Load balancers
- Search servers for searching through massive amounts of data
- Components involved in processing massive amounts of data
- Components running heterogeneous tech commonly known as web services etc.

### Why the need for so many tiers?
**Single responsibility principle** simply means giving only one responsibility to a component and letting it execute it perfectly.
**Separation Of concerns** kind of means the same thing, be concerned about your work only and stop worrying about the rest of the stuff. Keeping the components separate makes them reusable. Having loosely coupled components is the way to go. The approach makes scaling the service easy in the future when things grow beyond a certain level.

## Difference between layers & tiers
Don’t confuse tiers with the layers of the application. Some prefer to use them interchangeably. However, in the industry, layers of an application typically means the user interface layer, business layer, service layer, or the data access layer.

![layers-of-web-app](https://github.com/lymmm412/educative/blob/main/Scalability%20and%20System%20Design%20for%20Developers/Web%20application%20and%20Software%20Archecture/img/layers-of-web-app.jpg)

The layers mentioned in the illustration are at the code level. The difference between layers and tiers is that layers represent the conceptual organization of the code and its components, whereas, tiers represent the physical separation of components.
