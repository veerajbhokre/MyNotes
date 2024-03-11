# What is Microservices ?

- Microservices are small, independent, and loosely coupled services that can work together.
- Each service is a separate codebase, which can be managed by a small development team.
- Microservices communicate with each other by using well- defined APIs.
- Microservices can be deployed independently and autonomously.
- Microservices can work with many different technology stacks which is technology agnostic.
- Microservices has its own database that is not shared with other services.

# What is Microservices Architecture ?

- The microservice architectural style is an approach to developing a single application as a suite of small services,
  each running in its own process and communicating with lightweight mechanisms, often an HTTP or gRPC API.
- Microservices architecture decomposes an application into small independent services that communicate over
  well-defined APIs. Services are owned by small, self-contained teams.
- Microservices architecture is a cloud native architectural approach in which services composed of many loosely coupled
  and independently deployable smaller components.
- Microservices have their own technology stack, communicate to each other over a combination of REST APIs, are
  organized by business capability, with the bounded contexts.
- Following Single Responsibility Principle that referring separating responsibilities as per services.

# Microservices Characteristics

- Componentization via Services
- Organized around Business Capabilities
- Products not Projects -> You build it and you run it -> Full responsibility
- Smart endpoints and dumb pipes
- Decentralized Governance
- Decentralized Data Management
- Infrastructure Automation
- Design for failure

# Benefits of Microservices Architecture

- Agility, Innovation and Time-to-market
- Flexible Scalability
- Small, focused teams
- Small and separated code base
- Easy Deployment
- Technology agnostic, Right tool for the job
- Resilience and Fault isolation
  - Microservices are fault toleranced and handle faults correctly for example by implementing retry and circuit
    breaking patterns.
- Data isolation

# Challenges of Microservices Architecture

- Complexity
  - Each service is simpler, but the entire system is more complex. Deployments and Communications can be complicated
    for hundreds of microservices.
- Network problems and latency
  - Microservice communicate with inter-service communication, we should manage network problems. Chain of services
    increase latency problems and become chatty API calls
- Development and testing
  - Hard to develop and testing these E2E processes in microservices architectures if we compare to monolithic ones.
- Data integrity
  - Microservice has its own data persistence. Data consistency can be a challenge. Follow eventual consistency where
    possible
- Deployment
  - Deployments are challenging. Require to invest in quite a lot of devops automation processes and tools. The
    complexity of microservices becomes overwhelming for human deployment.
- Logging & Monitoring
  - Distributed systems are required to centralized logs to bring everything together. Centralized view of the system to
    monitor sources of problems.
- Debugging
  - Debugging through local IDE isn’t an option anymore. It won’t work across dozens or hundreds of services.

# The Database-per-Service Pattern

- Core characteristic of the microservices architecture is the loose coupling of services. every service should have its
  own databases, it can be polyglot persistence among to microservices.
- E-commerce application. We will have Product - Ordering and SC microservices that each services data in their own
  databases. Any changes to one database don’t impact other microservices.
- The service’s database can’t be accessed directly by other microservices. Each service’s persistent data can only be
  accessed via Rest APIs.

### Benefits of the Database-per-Service Pattern with Polygot Persistence

- Data schema changes made easy without impacting other microservices.
- Each database can scale independently.
- Microservices domain data is encapsulated within the service.
- If one of the database server is down, this will not affect to other services.
- Polyglot data persistence gives ability to select the best optimized storage needs per microservices.
- Ex:
  - Product service using NoSQL document database for storing catalog related data.
  - Shopping cart service using a distributed cache that supports its simple, key-value data store.
  - Ordering service using a relational database to handle the rich relational structure.
  - NoSQL databases able to massive scale and high availability, and also schemaless structure give flexibility.

---

# Main Considerations groups which we need to solve

- Decomposition – Breaking Down Services
- Communications
- Data Management
- Transaction Management
- Deployments
- Resilience

---

# 1. Decomposition of Microservices Architecture

### Why we need to Decompose ?

- What is the main reason behind the decomposition of microservices ? provide to Scale Independently.
- Main benefits of microservices are "Independent Scale and Deployments".
- Clearly identify microservices which parts could be required independent scale and deploy.
- Design Principle: Decompose services by scalability requirements
- Applications are consist of multiple modules or services, with different requirements for scaling.

### Microservices Decomposition Pattern:

**1.1 Decompose by Business Capability**

- In Microservices Architecture, split the application as a set of loosely coupled services.
- 2 Prerequisite of decomposition of microservices:
  - Services must be **cohesive**. A service should implement a small set of strongly related functions.
  - Services must be **loosely coupled** - each service as an API that encapsulates its implementation.
- "Decompose by Business Capability" pattern offer:
  - Define services corresponding to business capabilities.
  - A business capability is a concept from business architecture modeling.
  - A business service should generate value.

**1.2: Decompose by Subdomain**

- Services must be cohesive. A service should implement a small set of strongly related functions.
- Services must be loosely coupled - each service as an API that encapsulates its implementation.
- "Decompose by Subdomain" pattern offer:
  - Define services corresponding to Domain-Driven Design (DDD) subdomains.
  - DDD refers to the application’s problem space, the business as the domain. A domain is consists of multiple
    subdomains.
  - Each subdomain corresponds to a different part of the business.

**1.3: Bounded Context Pattern (Domain-Driven Design)**

- DDD - Bounded Context Pattern which is one of the main pattern that we mainly use when decomposing microservices.
- Domains are required high cooperation and have a certain complexity by nature are called collaborative domains
- DDD has 2 phases, **Strategic and Tactical DDD.**
- **Strategic DDD**, we define the large-scale model of the system, defining to the business rules that allow designing
  loosely coupling units and the context map between them.
- **Tactical DDD** focuses on implementation and provides design patterns that we can use to build the software
  implementation.
- Include concepts such as entity, aggregate, value object, repository, and domain service.
- DDD domain defines its own common language and divides boundaries into specific, independent components. Common
  language is called **ubiquitous language**, and independent units are called **bounded context**.

- DDD is solving a complex problem is to break the problem into smaller parts and focus on smaller problems that are
  relatively easy.
- A complex domain may contain sub domains. And some of sub domains can combine and grouping with each other for common
  rules and responsibilities.
- Bounded Context is the grouping of closely related scopes that we can say logical boundaries.
- Bounded context is the logical boundary that represents the smaller problem particles of the complex domain that are
  self-consistent and as independent as possible.

**Identify Bounded Context Boundaries for Each Microservices:**

- How we can identify Bounded Context ?
- To identify bounded contexts use DDD.
- In DDD, use the Context Mapping pattern for identification of bounded contexts.
- With Context Mapping Pattern, we can identify the whole bounded contexts in the application and with their logical
  boundaries.
- The solution is using Context Mapping.
- The Context Map is a way to define logical boundaries between domains.
- Identify the Bounded Contexts by talking to the domain experts and using some clues.
- Once defined the Bounded Contexts, iterate design, those are not immutable.
- Reshape your Bounded Contexts by talking to the domain experts and consider refactoring's with the changing
  conditions.
- Its crucial to discuss with domain experts to defining domains and sub domains.
- Evaluate Bounded Context with the domain experts will help you identify to microservices.
- Sub domains inside of the Bounded Context are representing same data but naming differently due to domain experts
  areas.
- Should discuss several domain experts for their expertise areas.

**A Bounded Context == A Microservice ?**

- No right answer to this question under all circumstances.
- Bounded Context can create more than one Microservice.
- Decision to be made based on the microservice's need for scalability and independence.
- Since Bounded Context defines the boundaries of the domain, a Microservice determines the technical and organizational
  boundaries.
- Similar to Microservices, Bounded Contexts are autonomous and responsible by certain domain capability.
- Context Mapping and the Bounded Context pattern are good approaches for identifying microservices.

**Using Domain Analysis to Model Microservices**

- Microservices should be designed by business capabilities and should have loose coupling and autonomous services.
- We can change a particular microservices without affecting other services. Each service can be change independently.
- Domain-driven design (DDD) provides a set of methodology that we can follow the principles and create a well-designed
  microservices.
- Follow DDD-Bounded Context which following Context Mapping Pattern and decompose by sub domain models patterns.

**Checklist After Decompose Microservices**

- Microservice should do "one thing"
- Microservice size should not too big and not too small
- Avoid Chatty Communication
  - When you splitting functionality into two services, if those services becomes overly chatty communications, then its
    good to combine them into 1 service.
- No Locking Dependencies
  - If your services has inter-service dependencies more than 2 or 3, and if those are required to move and deploy
    together that means there are pain points of your design and its good to re-think again.
- It should always be possible to deploy a microservice without re-deploying any other services. Services should not be
  tightly coupled, and can evolve independently.

**Example:**

Identify steps:

- Requirements and Modelling
- Identify User Stories
- Identify the Nouns in the user stories
- Identify the Verbs in the user stories

Understand E-Commerce Domain: Functional Requirements:

- List products
- Filter products as per brand and categories
- Put products into the shopping cart
- Apply coupon for discounts and see the total cost all for all of the items in shopping cart
- Checkout the shopping cart and create an order
- List my old orders and order items history

Understand E-Commerce Domain: User Stories (Use Cases):

- As a user I want to list products
- As a user I want to filter products as per brand and categories
- As a user I want to put products into the shopping cart so that I can check out quickly later
- As a user I want to apply coupon for discounts and see the total cost all for all of the items that are in my cart
- As a user I want to checkout the shopping cart and create an order
- As a user I want to list my old orders and order items history
- As a user I want to login the system as a user and the system should remember my shopping cart items

Analysis E-Commerce Domain - Nouns and Verbs:

- As a user I want to **list** `products`
- As a user I want to be able to **filter** `products` as per `brand` and `categories`
- As a user I want to **see** the supplier of `product` in the product detail screen with all characteristics of product
- As a user I want to be able to **put** `products` that I want to **purchase** in to the `shopping cart` so I can check
  out
- As a user I want to **see** the total cost all for all of the `items` that are in my `cart` so that I see if I can
  afford to buy
- As a user I want to **see** the total cost of each `item` in the `shopping cart` so that I can re-check the price for
  items
- As a user I want to be able to **specify** the `address` of where all of the products are going to be sent to
- As a user I want to be able to **add** a note to the `delivery address` so that I can provide special instructions
- As a user I want to be able to **specify** my `credit card` information during **check out** so I can pay for the
  items
- As a user I want system to **tell** me how many items are in `stock` so that I know how many items I can purchase
- As a user I want to **receive** order confirmation email with `order` number so that I have proof of purchase
- As a user I want to **list** my old `orders` and `order items history`
- As a user I want to **login** the system as a `user` and the system should remember my shopping cart items

Analysis E-Commerce Domain - Nouns:

- Customer
- Order
- Order Details
- Product
- Shopping Cart
- Shopping Cart Items
- Supplier
- User
- Address
- Brand
- Category

Analysis E-Commerce Domain - Verbs:

- List products applying to paging
- Filter products by brand, category and supplier
- See product all information in the details screen
- Put products in to the shopping cart
- See total cost for all of the items
- See total cost for each item
- Checkout order with purchase steps
- Specify delivery address
- Specify delivery note for delivery address
- Specify credit card information
- Pay for the items
- Tell me how many items are in stock
- Receive order confirmation email
- List the order and details history
- Login the system and remember the shopping cart items

![img.png](pics/img.png)

**Object Responsibility Diagram:**

![img_1.png](pics/img_1.png)

**Main Use Case of Our E-Commerce Application:**

![img_2.png](pics/img_2.png)

**Identifying and Decomposing Microservices for E-Commerce:**

![img_3.png](pics/img_3.png)

---

# Communications

Main Reasons of Why should use API Gateway:

- Client Can't Manage Communications
  - The client applications can try to handle multiple calls to microservice endpoints, not manageable when it comes to
    communicates with different protocols like http, GraphQL, gRPC and WebSocket.
- Complex Client Code
  - Cause to lots of requests to the backend services, create possible chatty communications. Increases latency and
    complexity on the UI side. Becomes complex client code.
- Strong Coupling
- Cross-cutting Concerns
  - Authentication, Authorization, Rate Limiting, SSL certifications, Logging, Monitoring, Load Balancing and Circuit
    Breaker. Implementing all these cross- cutting concerns for every microservice is not a good solution.
- Protocol Exchanges
  - Communication required to use different protocols like exposing HTTP Rest API but continue to gRPC for internal
    communications or exposing WebSocket protocol. Requests must be translate to the other protocols afterwards
- Async Communication Requirements
  - Client application can't use async communication due to not supported AMQP protocols. Microservices uses async
    communication in order to decouple communication as soon as possible. Hard to implement event-driven publish-
    subscribe model.
- Payload Changes for Different Clients
  - Web, Mobile and other client applications might be required different payloads when communicating with internal
    services. It requires to optimize data responses from services tailored with client applications.

API Gateway Patterns:

- Use API Gateways between client and internal microservices and it is a single point of entry to the client
  applications.
- API Gateway sits between the client and multiple backend services and manage routing to internal microservices.
- API Gateways can handle that Cross-cutting concerns like authentication, authorization, protocol translations, Rate
  Limiting, Logging, Monitoring, Load Balancing.
- API Gateway Patterns:
  - Gateway Routing pattern
  - Gateway Aggregation pattern
  - Gateway Offloading pattern

Gateway Routing pattern:

- Route requests to multiple microservices with exposing a single endpoint.
- Useful when expose multiple services on a single endpoint and route them to internal backend microservices based on
  the request.
- The client needs to consume several microservices, Gateway Routing pattern offers to create a new endpoint that handle
  the request and route this request for each service.
- E-commerce application might provide services such as search Customers, Shopping cart, discount, and order history.
- If one of microservices are changed, the Client doesn't know anything and not need to change any code on client side,
  the only changes will be configuration routing changes.
- It abstracts your backend microservices from the client applications, with allowing to keep client codes simple, even
  backend services are changed on behind the API Gateway.
- Deploy microservices APIs with blue/green or canary deployments with multiple API versions of same microservices, you
  can gradually shift your request to new API with using the Gateway Routing Pattern.
- Gateway Routing Pattern gives you the flexibility to use different API versions routing to the requests.
- If new version of API has got exceptions, it can be quickly reverted back by making only a configuration change.
- Use Application Layer 7 routing to route the request to the internal services.

Gateway Aggregation Pattern:

- API Gateway service that provide to aggregate multiple individual requests towards to internal microservices with
  exposing a single request to the client.
- Use if client application have to invoke several different backend microservices to perform its logic.
- Client applications need to make multiple calls to different backend microservices, this leads to chattiness
  communications and impact the performance.
- If new service added into use cases, then client need to send additional request that increase the network calls and
  latency.
- To abstract complex internal backend service communication from the client applications.
- Dispatches requests to the several backend services, then aggregates the results and sends them back to the requesting
  client.

![img.png](pics/wetey.png)

Gateway Offloading Pattern:

- Gateway Offloading Pattern offers to combine commonly used shared functionalities into a gateway proxy services.
- Shared functionalities can application development by moving shared service functionality into centralized places.
- Authentication, Authorization, Rate Limiting, SSL certifications, Logging, Monitoring, Load Balancing.
- Implementing cross-cutting concerns for every microservice is not a good solution.
- Gateway Offloading Pattern offers to manage all those Cross-cutting Concerns into API Gateways.
- Simplify the development of microservices by removing the cross-cutting concerns on services to maintain supporting
  resources, allow to focus on the application functionality.

API Gateway Pattern - Summarized:

- API gateway locate between the client apps and the internal microservices.
- Working as a reverse proxy and routing requests from clients to backend services and provide cross-cutting concerns
  like authentication, SSL termination, and cache.
- Several client applications connect to single API Gateway possible to single-point-of-failure risk.
- If these client applications increase, or adding more logic to business complexity in API Gateway, it would be anti-
  pattern.
- Best practices is splitting the API Gateway in multiple services or multiple smaller API Gateways: BFF-Backend-
  for-Frontend Pattern.
- Should segregated based on business boundaries of the client applications.

Main Features of API Gateway Pattern:

- **Reverse Proxy and Gateway Routing** Reverse proxy to redirect requests to the endpoints of the internal
  microservices. Using Layer 7 routing for HTTP requests for redirections. Decouple client applications from the
  internal microservices. Separating responsibilities on network layer and abstracting internal operations.
- **Requests Aggregation and Gateway Aggregation** Aggregate multiple internal microservices into a single client
  request. Client application sends a single request to the API Gateway and it dispatches several requests to the
  internal microservices and then aggregates the results and sends back to the client application in 1 single response.
  Reduce chattiness communication.
- **Cross-cutting Concerns and Gateway Offloading** Best practice to implement cross-cutting functionality on the API
  Gateways. Cross-cutting functionalities can be; Authentication and authorization, Service discovery, Response caching,
  Retry policies, Circuit Breaker, Rate limiting and throttling, Load balancing, Logging, tracing, IP allowlisting.

Backends for Frontends Pattern - BFF:

- Backends for Frontends pattern basically separate API Gateways as per the specific frontend applications.
- Single API Gateway makes a single-point-of failure.
- BFF offers to create several API Gateways and grouping the client applications according to their boundaries.
- A single and complex API Gateway can be risky and becoming a bottleneck into your architecture.
- Larger systems often expose multiple API Gateways by grouping client type like mobile, web and desktop functionality.
- Create several API Gateways as per user interfaces to provide to best match the needs of the frontend environment.
- BFF Pattern is to provide client applications has a focused interfaces that connects with the internal microservices.

# Service-to-Service Communications between Backend Internal Microservices:

- Created API Gateways and separate this API Gateways following the BFF Pattern.
- Sync request comes from the clients and goes to internal microservices over the API Gateways.
- What if the client requests are required to visit more than one internal microservices ? How we can manage internal
  microservice communications ?
- Best practice is reducing inter-service communication as much as possible.
- In some cases, we can't reduce these internal communications due to operation need to visit several internal services.
- Client send query request to internal microservices to accumulate some data in the sceen page of frontend.
- User addItem into shopping cart, these operation need to get data from Product and Price microservices.
- Synchronous communcation over the API Gateways which is the Request/Response Messaging.
- The client send http request to the microservices for querying or adding item into Shopping Cart:
  - 1- The client send request to API Gateway
  - 2- API Gateway dispatch request to ShoppingCart
  - 3- SC need to extend information by sending sync request to Product and Pricing microservices
- Internal calls makes coupling each microservices.
- If one of the microservices is down, it can't return data to the client so its not any fault-tolerance.
- If service calls are much then a few HTTP calls to multiple microservices, than it goes to un-manageable situation.
- Place Order Use Case: client checkout shopping cart, start order fulfillment processes.
- Request/Response Sync Messaging end up with 6 sync chain HTTP Request.
- Increase latency and negatively impact the performance, scalability, and availability.
- What if the step 5 or 6 is failed ?
- We can apply 2 way to solve this issues:
  - 1- **Change microservices communications to async way**
  - 2- **Use Service Aggregator Pattern to aggregate some query operations in 1 API Gateway.**

![img.png](pics/sdf.png)

**Service Aggregator Pattern:**

- Service Aggregator Pattern is receives a request from the client or API Gateway.
- Dispatches requests of multiple internal backend microservices.
- Combines the results and responds back to the initiating request in 1 response structure.
- Reduces chattiness and communication overhead between the client and microservices
- AddItem aggregates request data from to several back-end microservices: Product - SC and Pricing.
- Isolates the underlying addItem operation that makes calls to multiple back-end microservices.
- Centralizing its logic into a specialized microservice.

![img_1.png](pics/ijehrukhshjfsvg.png)

**Service Registry/Discovery Pattern:**

- Microservice Discovery Patterns and Service Registry provide to register and discover microservices in the cluster.
- Why We Need Service Discovery Pattern ? When deploying and scaling microservices, more microservices and instances
  could be added to the system to provide scalability to the distributed application.
- Service locations can change frequently, more dynamic configuration is needed for the microservice architecture.
- IP Addresses and network locations are dynamically assigned and often change due to auto-scaling features.
- Service Registry and Discovery pattern allows to find the network locations of microservices without injecting or
  coupling services.
- To find the network locations of microservices, the service discovery pattern is used in microservices applications.
- It will provide to register and discover microservices in the cluster.
- API Gateways for routing the traffic with client and internal microservices. How API Gateways access the internal
  backend microservices ?
- Service discovery pattern uses a centralized server for «service registry» to maintain a central view of microservices
  network locations.
- Services update their locations in the service registry at fixed intervals. Clients can connect to the service
  registry and fetch the locations of microservices.
- There are 2 main service discovery patterns: - Client-side service discovery
- Server-side service discovery

# Microservices Asynchronous Message- Based Communication

Synchronous communication:

- Synchronous communication is using HTTP or gRPC protocol for returning synchronous response.
- The client sends a request and waits for a response from the service.
- The client code block their thread, until the response reach from the server.
- The synchronous communication protocols can be HTTP or HTTPS.
- The client sends a request with using http protocols and waits for a response from the service.
- The client call the server and block client their operations.
- The client code will continue its task when it receives the HTTP server response.

Asynchronous Communication:

- The client sends a request but it doesn't wait for a response from the service. The client should not have blocked a
  thread while waiting for a response.
- AMQP (Advanced Message Queuing Protocol)
- Using AMQP protocols, the client sends the message with using message broker systems like Kafka and RabbitMQ queue.
- The message producer does not wait for a response.
- Message consume from the subscriber systems in async way, and no one waiting for response suddenly.
- If there is busy interactions in communication across multiple microservices, then use asynchronous messaging
  platforms.
- Message brokers are responsible for handling the message sent by the producer service in async messaging-based
  communication.
- If the consumer service is down at the moment, the broker might be configured to retry as long as necessary for
  successful delivery.
- Messages can be persisted if required or stored only in memory.
- Message broker is responsible for delivering the message.
- No longer necessary for both microservices to be up and running for successful communication.
- Async messaging provides loosely couple communication.

Benefits of Asynchronous Communication:

- New Subscriber Services Adding new services is very simple. We can easily subscribe to message that we want to
  receive. The producer doesn’t need to be know about subscribers, we can remove and add subscribers without affecting
  producer service.
- Scalability With async communications we can easier to manage scalability issues, can scale producer, consumer and
  message broker system independently. Scale services according to incoming messages into event bus system. Kubernetes
  KEDA Auto-scalers.
- Event-driven Microservices With async communication, we can provide event-driven architectures which is best way to
  communicate between microservices.
- Retry mechanisms Brokers can retry to sending message and keep trying automatically without any custom solutions.

Challenges of Asynchronous Communication:

- Single Point of Failure - Message Broker The message broker becomes a single point of failure. We should not rely of
  all communication with a single node of message brokers, instead we should scale it and use hybrid communication with
  sync and async in your cases.
- Debugging difficult to debug issues with async communication, it can be hard to trace the flow of a single operation
  across service boundaries. debugging of the flow and the payload of events takes so many times and hard to debug at
  the same time.
- At-least-once delivery and Not Guarantee an order of messages Mostly Brokers use at-least-once delivery and not
  Guarantee order of messages. Should embrace these message delivery mechanism with applying idempotency consumers and
  not designing FIFO requires cases.


- Use Asynchronous message-based communication when you have multiple microservices are required to interact each other
  without any dependency.
- Asynchronous message-based communication is works with events.
- Events can place the communication between microservices: Event-driven communication.
- If any changes happens in microservices, it is propagating changes across multiple microservices as an event.
- Events consumed by subscriber microservices.
- Event-driven communication bring us Eventual consistency.

2 Type of Asynchronous Messaging Communication:

- Single-receiver Message-based Communication (1-1 model/ point to point model)
- Multi-receiver Message-based Communication (1-Many model/ publish-subscribe model)

Single-receiver Message-based Communication:

- one-to-one (queue) or point-to-point communications.
- If you send 1 request to the specific consumer, and this operation will take long time, use this Single-receiver async
  one-to-one communication.
- Single producer and single receiver; implementation as a Command Pattern.

Multiple-receiver Message-based Communication:

- Publish/subscribe mechanisms that has multiple receivers.
- Producer service publish a message and it consumes from several microservices by subscribing message on the message
  broker system.
- Publisher don't need to know any subscriber, no any dependecy with communication parties.
- one-to-many (topic) implementation has Multiple receivers. Each request can be processed by zero to multiple
  receivers.
- Publish/subscribe used in patterns Event-driven microservices architecture.
- Message broker system is publishing events between multiple microservices, subscribing these events in an async way.
- Messages are available to all subscribers and the topic can have more than one subscriber.
- The message remains persistent in a topic until they are deleted.
- Kafka, RabbitMQ or Amazon SNS and EventBridge.
- Event-driven Architecture, CQRS pattern, event storming, eventual consistency principles.

Fan-Out & Message Filtering with Publish/Subscribe Pattern:

- Fan-out is a messaging pattern; ‘fanned out’ to multiple destination in parallel.
- Each of destinations can work and process this messages in parallel.
- Publisher/subscriber model to define a topic which is logical access point to enabling message communication with
  asynchronously.
- Publisher sends the message to the topic, message is immediately fanned out to all subscribers of this topic.
- Each service can operate and scale independently and individually that completely decoupled and asnycronously.
- The publisher and the subscribers don’t need to know who is publishing / consuming this message that is broadcasting.
- Deliver the same message to multiple receivers is to use the Fanout Publish/Subscribe Messaging Pattern.

![img_2.png](pics/joieruoir.png)

---

# Microservices Database Management Patterns and Principles

- Managing a microservices database is challenging job, should have a strategy.
- Challenging to implement queries and transactions that visits to several microservices.

### Microservices Database Management Patterns and principles:

- The Database-per-Service pattern
- The API Composition pattern
- The CQRS pattern
- The Event Sourcing pattern
- The Saga pattern
- The Shared Database anti-pattern

**Overview of patterns:**

**1. The Database-per-Service Pattern:**

- In order to be a loose coupling of services, each microservice should have its own private database. Design database
  architecture for microservices.
- Microservices should be loosely coupled, scalable, and independent. When shifting to the monolithic architecture to
  microservices architecture, should decomposes databases.
- Distributed data model with many smaller databases for particular microservice.
- Data schema changes can perform without any impact on other microservices, Changes to an individual database don’t
  impact other services.
- There isn’t a single point of failure in the application, the application is becomes more resilient.
- Individual databases are easier to scale, if 1 service peek the requests that only that microservice can scale
  independently.
- Separating databases can gives us to ability to pick the best optimized database for our microservices.
- Can choices include relational, document, key-value, and even graph-based data stores.
- Using the most efficient database depending on the service requirements and functionality.
- Drawback:
  - Services need a communication method to exchange data, inter-service communication.
  - Each service must provide a clear API, that need to make resilience of these communications like applying retry and
    circuit breaker patterns.
  - Distributed transactions across microservices can negatively impact consistency and atomicity.
  - Complex queries, no simple way to execute join queries on multiple data stores.

**2. The Shared Database Anti-Pattern:**

- You can create a single shared database with each service accessing data using local ACID transactions. But it is
  against to microservices nature and will cause serious problems in the future of applications.
- If we don’t follow Database-per-Service pattern and use Shared Database for several microservices = Anti-Pattern
- It is against to microservices nature and will cause serious problems in the future of applications.
- When using a shared database, the microservices lose their core properties: scalability, resilience, and independence.
- You will face to develop big a few monolithic applications instead of microservices.
- Shared database can block microservices due to single- point-of-failure.
- If shared database seems to be the best option for the microservices project, should re-think that we really need the
  microservices, the monolith would be the better choice.
- Benefits:
  - Transaction management, that we don't need to span the transactions over the microservices.
  - Decrease duplicate data. Since data is fully constrained, we can easily execute complicated queries with joins.
  - Able to follow ACID. Consistency of data and state easily manage when process fails.
- Drawbacks:
  - Microservices with shared databases can’t easily scale.
  - Shared database will become a single point of failure.
  - Microservices won’t be independent in terms of development and deployment.

The API Composition Pattern:

Retrieving data from several services also need a set of patterns and practices. When implements a query by invoking
several microservices, we will follow the API Composition, Gateway Aggregation patterns for combining the results.

The CQRS Pattern:

The command query responsibility segregation (CQRS) is provide to seperate commands and queries database in order to
better perform querying several microservices.

The Event Sourcing Pattern:

The Event Sourcing pattern basically provide to accumulate events and aggregates them into sequence of events in
databases. We can replay at certain point of events.

The Saga Pattern:

Transaction management in really hard when it comes to microservices architectures. We use Saga Pattern to implementing
transactions between several microservices and maintaining data consistency.

---

# Database Choises for microservices

### Polyglot Persistence

- Microservices enables using different kinds of data storing technologies.
- Each development team can choose the persistence technology that suits the needs of their service best.
- Martin Fowler - Polyglot persistence will come with a cost - but it will come because the benefits are worth it.
- When relational databases are used inappropriately, they give damaged on application development.
- Example of looked up page elements by ID, much better suited to a key-value No-SQL databases than relational
  databases.

## How to Choose a Database for Microservices ?

- Relational Databases - RDBMS
  - Relational databases provides storing data into related data tables.
  - Relational database tables have a fixed schema, use SQL to manage data and support transactions with ACID
    principles.
  - A table uses columns and rows for storing the actual data. Each table will have a column that must have unique
    values—known as the primary key.
  - The main advantages of Relational database is ACID compliance. If one change fails, the whole transaction will fail.
  - Example of relational databases are Oracle, MS SQL Server, MySQL, PostgreSQL.

- No-SQL Databases (Non-Relational Databases)
  - No-SQL databases has different types of stored data and data models: Document, Key-value, Graph-based, Column-based
    databases.
  - Ease-of-use, scalability, resilience, and availability characteristics.
  - NoSQL databases stores unstructured data, and this gives huge performance advantage.
  - NoSQL stored unstructured data in key-value pairs or JSON documents.
  - No-SQL databases don't provide ACID guarantees.
  - Drawback is transaction management.

No-SQL Document Databases:

- Document databases stores and query data in JSON-based documents.
- Data and metadata are stored hierarchically.
- Objects are mapping to the application code.
- Don’t have to run JOINs or decompose data across tables.
- Scalability, document databases can distributed very well.
- Best choise for content management and storing catalogs.
- I.e. products data can store in document database for e- commerce applications.
- Example Document Databases: MongoDB and Cloudant.

No-SQL Key-Value Databases:

- Data is storing as a collection of key-value pairs in Key- value NoSQL database.
- Data is represented as a group of key-value in the database.
- Best choise for session-oriented applications.
- I.e. storing customer basket data into key-value database.
- Example Key-Value Databases: Redis, Amazon DynamoDB, Azure CosmosDB, Oracle NoSQL Database.

No-SQL Column-Based Databases:

- Column-based databases also known Wide-Column Databases.
- Data is stored in columns, by this way, it can access necessary data more faster than if we compare to storing data in
  rows.
- If you select mostly same columns in your databases, its good to use this databases.
- It doesn't scanning the unnecessary information in a whole row.
- Column-based databases can scale by columns independently.
- Columns could be different database servers.
- I.e. building a Data warehouse, Big Data processing.
- Apache Cassandra, Apache HBase or Amazon DynamoDB, Azure CosmosDB.

No-SQL Graph-Based Databases:

- Graph-based databases is stores data in a graph structure into node, edge, and data properties.
- Data entities are connected in nodes.
- The main benefit of a graph-based databases is to store and navigate graph relationships.
- I.e. fraud detection, social networks, and recommendation engines.
- Example of Graph-based databases are OrientDB, Neo4j, and Amazon Neptune.

When to Use Relational Databases ?

- Read Requirements, Complex Join Queries Relational Database has a fixed schema. When relationships between tables are
  important and data is highly structured and requires referential integrity.
- Can work with complex queries, table joins and reports on normalized data models.
- Relational Database supports a powerful SQL query language.
- Deployments, Centralized Structure Relational Databases will be deployed to large and one or few locations. Relational
  database has centralized structure.
- Relational databases have a single point of failure with failover.
- Relation database is deployed in vertical fashion.

When to Use No-SQL Databases ?

- Flexible Schema, Dynamic Data NoSQL Database has no fixed schema. Allows to add or remove attributes into their model
  with dynamically. When your data is dynamic and frequently changes.
- Use case of implement an IoT platform that stores data from different kinds of sensors with frequently changed the
  attributes of your data.
- Un-predictable Data, High Workload Volume When you have high volume workloads and needs to horizontal scale with low
  latency. NoSQL databases have been designed for the cloud that naturally good for horizontal scaling.
- NoSQL Databases prioritize partition tolerance that designed for handling large amount of data or data coming in high
  velocity.
- Frequently Change Data and Read Requirements When data is dynamic and frequently changes and Relationships are de-
  normalized data models and Data retrieve operations are simple and performs without table joins.
- Data Consistency, BASE Model - Basically Available, Soft State, Eventually Consistent NoSQL Database is only
  eventually consistent and don't support transactions, focus on high volume data and horizontal scaling.
- Write Performance Requirements NoSQL Database compromise consistency to achieve fast write performance, offers to fast
  write operations with Eventual consistency.
- Not Good for Complex Join Queries NoSQL Databases perform best when data is stored in the same format not require to
  relation and join operations.
- Deployments, De-centralized Structure NoSQL is scale horizontally so data is replicated across different geographical
  zones and provides better control over consistency, availability, and performance.
- NoSQL databases have no single point of failure, has decentralized structure, gives both read and write scalability,
  deployed in horizontally.

Focus The Data Type That Need to Store , Consider the type of data that you have:

- Store JSON documents in No-SQL Document database.
- Put transactional data into a Relational SQL database.
- Use a time series data base for telemetry databases.
- Choose Blob Data Storage for blob datas.
- Put application logs into Elastic Search Databases.

Trade-offs between Availability and Consistency:
Understand the Trade-offs between Availability and Consistency. Should prefer High Availability over strong consistency
as soon as possible to scale horizontally. (the CAP theorem)

Transactional Boundaries Between Microservices:
Consider business Transactional Boundaries Between Microservices, that data need to consistent across those
microservices. Prepare for compensating transactions in case of fail.


---

# Microservices Data Management - Commands and Queries

Problem:

- Cross-Service Queries with Complex JOIN operations
- Read and write operations at scale
- Distributed Transaction Management

Solutions:

- Microservices Data Query Pattern and Best Practices
- Materialized View Pattern
- CQRS Design Pattern
- Event Sourcing Pattern

What is Microservices Cross-Service Queries?

- Monolithic architectures, its very easy to query different entities, Querying data across multiple tables is
  straightforward.
- Microservices architectures uses polyglot persistence, has different databases, need strategy to manage queries.
- What if the client requests are visit more than one internal microservices ?
- E-commerce application we have product, basket, discount, ordering microservices that needs to interact each other to
  perform customer use cases.
- Integrations are querying each services data for aggregation or perform logics.

How can we manage these cross-services queries ?

Assumptions:

- Direct HTTP Communication
  - Not a good solution that makes coupling each microservices, and loose power of microservices independency.
- Async Communication
  - The best practice is reducing inter-service communication as much as possible and use async communication. Can't
    reduce these internal communications due to customer requirement.
- Client send query request to internal microservices to accumulate some data.
- Those query request wait immediate response so we can't proceed with async communication.
- Transient errors, Network congestion or any overloaded microservice can result in long-running and failed operations.
- Materialized View Pattern
  - Reduce inter-service communication and provide sync response.

Before we understand more see below examples of Cross queries:

![img.png](pics/sdghsfgsdfg.png)

Microservices Cross-Service Queries Chain Queries – Get Order with All Details:

![img_1.png](pics/sdfsgdsfhghj.png)

Cross-Service Query Solutions in Microservices:

Weak Solutions:

- Sync Communication: Use Service Aggregator Pattern but it increase coupling and latency.
- Async Communication: Provide decoupling but query request are waiting immediate response.

Solutions:

- Materialized View Pattern
- CQRS Design Pattern

### Materialized View Pattern

![img_2.png](pics/gfdhdfgh.png)

- Instead of querying the Product Catalog and Pricing services, SC maintains its own local copy of that data.
- With Materialized View Pattern, even if the Catalog and Pricing services are down, Shopping Cart can continue.
- Broke the direct dependency of other microservices and make faster the response time, help efficient querying and
  improve application performance.
- Generate pre-populated views of data, more suitable format for querying and provide good query performance.
- Includes joining tables and combining data entities and calculated columns and execute transforms.
- Views are disposible and can rebuilt from the source.
- With appying Materialized View Pattern, we have duplicated data into our system.
- Duplicating data is not a anti-pattern, have strategically duplicating our data for microservice communications.
- Only one service can be a data ownership.
- _How and when the denormalized data will be updated ?_
  - When the original data changes it should update into sc microservices.
  - Need to synchronize the read models when the main service of data is updated.
  - Solve with using asynchronous messaging and publish/subscribe pattern.
  - Publish an event and consumes from the subscriber service to update its denormalized table.
  - Using a scheduled task, an external trigger, or a manual action to regenerate the table.

### CQRS (Command Query Responsibility Segregation)

![img_3.png](pics/vchgfdhsdhgdfh.png)

- CQRS design pattern in order to avoid complex queries to get rid of inefficient joins.
- Separates read and write operations with separating databases.
- Commands: changing the state of data into application.
- Queries: handling complex join operations and returning a result and don't change the state of data into application.
- Large-scaled microservices architectures needs to manage high-volume data requirements.
- Single database for services can cause bottlenecks.
- Uses both CQRS and Event Sourcing patterns to improve application performance.
- CQRS offers to separates read and write data that provide to maximize query performance and scalability.

CQRS – Read and Write Operations:

- Monolithic has single database is both working for complex join queries, and also perform CRUD operations.
- When application goes more complex, this query and CRUD operations will become un-manageable situation.
- Application required some query that needs to join more than 10 table, will lock the database due to latency of query
  computation.
- Performing CRUD operations need to make complex validations and process long business logics, will cause to lock
  database operations.
- Reading and writing database has different approaches, define different strategy.
- «Separation of concerns» principles: separate reading database and the writing database with 2 database.
  - Read database uses No-SQL databases with denormalized data.
  - Write database uses Relational databases with fully normalized and supports strong data consistency.
- If our application is mostly reading use cases and not writing so much, it is read-incentive application.
- Read and write operations are asymmetrical and has very different performance and scale requirements.
- To improve query performance, the read operation perform queries from a highly denormalized materialized views to
  avoid expensive repetitive table joins and table locks.
- Write operation which is command operation, can perform commands into separate fully normalized relational database.
- Supporting ACID transactions and strong data consistency.
- Commands: task-based operations like "add item into shopping cart" or "checkout order".
- Commands can be handle with message broker systems that provide to process commands in async way.
- Queries: never modify the database, always return the JSON data with DTO objects.

CQRS – Synchronization with Read and Write DB:

- Keep sync both read and write databases.
- Publishes an event that subscribe from Read Database and update the read table accordingly.
- Synchronization handles with async communication using message brokers.
- This creates Eventual Consistency principle.
- The Read database eventually synchronizes from the Write database.
- Some lag in the process due to async communication with message brokers that applies publish/subscribe pattern.
- Welcome to Eventual consistency.

Benefits of CQRS:

- Scalability When we separate Read and Write databases, we can also scale these separate databases independently. Read
  databases follows denormalized data to perform complex join queries.
- If application is read-incentive application, we can scale read database more then write database.
- Query Performance The read database includes denormalized data that reduce to comlex and long-running join queries.
  Complex business logic goes into the write database. Improves application performance for all aspects.
- Maintability and Flexibility Flexibility of system that is better evolve over time and not affected to update commands
  and schema changes by separating read and write concerns into different databases.
- Better implemented if we physically separate the read and write databases.

Drawbacks of CQRS:

- Complexity : CQRS makes your system more complex design. Strategically choose where we use and how we can separate
  read and write database.
- Eventual Consistency : The read data may be stay old and not-updated for a particular time. So the client could see
  old data even write database updated, it will take some time to update read data due to publish/subscribe mechanism.
- We should embrace the Eventual Consistency when using CQRS, if your application required strong consistency than CQRS
  is not good to apply.

Best Practices for CQRS:

- Best practices to separate read and write database with 2 database physically.
- Read-intensive that means reading more than writing, can define custom data schema to optimized for queries.
- Materialized View Pattern is good example to implement reading databases.
- Avoid complex joins and mappings with pre-defined fine- grained data for query operations.
- Use different database for reading and writing database types.
- Using No-SQL document database for reading and using Relational database for CRUD operations.

How to Sync Read and Write Databases in CQRS ? :

- Event-Driven Architecture, when something update in write database, publish an update event with using message broker
  systems, consume by the read database and sync data according to latest changes.
- Creates a consistency issue, the data would not be reflected immediately due to async communication with message
  brokers.
- «Eventual Consistency» The read database eventually synchronizes with the write database, and take some time to update
  read database in the async process.
- Take read database from replicas of write database. applying Materialized View Pattern can significantly increase
  query performance.
- Event Sourcing Pattern is the first pattern we should consider to use with CQRS.
- CQRS is using with "Event Sourcing Pattern" in Event-Driven Architectures.

![img_4.png](pics/sgsdfgtgrh.png)

---

# Microservices Distributed Transactions

### Transaction Fundamentals

- Transaction is the context of database operations that should act to be single unit of work.
- Should fully complete the operation or if something fail, it should rollback the whole operation and go back to
  consistent state.
- ACID - Atomicity, Consistency, Isolation, and Durability
- If one change fails on database, the whole transaction is going to fail, and the database should remain in the
  previous state.
- Relational Databases are providing ACID principles to ensure Data Consistency.
- If you need strong consistency, its good to choose Relational Databases.

### ACID Principles

- ACID - Atomicity, Consistency, Isolation, and Durability
- Atomicity All operations are executed successfully or everything fails and rollback together.
- Consistency The data in the database is kept in a valid and consistent state, after any operations on data.
- Isolation Separated transactions running concurrently and they can not interfere with each other.
- Durability After a transaction is committed, the changes are stored durably, for example persisted in a database
  server.
- Example: Money transfer transaction.

### Transaction Management of Monolithic Architecture

- Transaction management in Monolith architecture is quite easy compared to Microservice Architecture. Many frameworks
  contains mechanism for transaction management.
- These mechanism have a single database of the whole application. They are developed for scenarios where all
  transactions are running on a single context.
- Simply commit and rollback operations with these mechanism in monolith architectures.
- Transactions operated in the transaction scope are kept in memory without writing to the database until they are
  committed, and if a Rollback is made at any time, all transactions in the scope are deleted from memory and the
  transaction is canceled.
- When Commit is written to the database, the transaction is completed successfully.

### Microservices Distributed Transactions

- How to perform transactional operations across microservices and which patterns we should apply ?
- Querying data and run complex join queries across microservices is not easy but implement transactional operations
  across microservices is more complex.
- We should handle distributed transaction managements on microservices by manually implementing some patterns and
  practices.
- Distributed transaction managements on microservices, mostly working with eventual consistency.
- Microservices has its own database and communicates each other with exposing APIs, it makes harder to perform
  end-to-end business cases across multiple microservices when trying to keep data consistency.
  **- How to achieve consistency across multiple microservices ?**

### How to achieve Data Consistency across multiple microservices ?

- CAP Theorem: need to sacrifice ACID strong consistency to the High Availability and Partition Tolerance.
- Most microservice-based applications are choosing high availability and high scalability against to strong
  consistency.
- Microservices sacrifice strong consistency and follow with eventual consistency to get benefits of microservices
  availability and scalability.
- NoSQL database are mostly using in microservices because they can easily and horizontally scale in distributed
  environments.
- Must use asynchronous event-driven communication and publish/subscribe pattern following to eventual consistency.
- When product price updated, Product service publish changes with Product price changed event to the Event Bus, that
  subscribe by ShoppingCart service with following eventual consistency principle.
- This process should be resilient and idempotent for redundant event processing; Microservices Resillience.

### Microservices Transactional Boundaries

- We have several Bounded Context and Microservices for our e- commerce application:
- Customer-User, Product, Shopping Cart, Discount
- Ordering, Payment, Inventory, Shipment, Notification
- Some of these bounded context need to organize and communicate each other to perform End-to-end business use cases.
- After identifying Bounded Contexts and microservices, should also identify our microservices transactional boundaries.
  **- What are Microservices Transactional Boundaries ?**

### What are Microservices Transactional Boundaries ?

- Transactional Boundaries are smallest unit of atomicity that need to provide consistency between services.
- In distributed architecture we can't provide ACID principles.
- Should define Transactional Boundaries and perform E2E use cases to try to keep data consistency.
- Use asynchronous event-driven communication and publish/subscribe pattern following to eventual consistency.
- Not enough for complex and long E2E business case that required to visit more that 5 microservices in a distributed
  environment.
- Microservices Transactional Boundaries is identify minimum smallest unit of atomicity use cases and design
  communication between those boundaries.

### E-Commerce Microservices Transactional Boundaries

![img.png](pics/img32454.png)

### Saga Pattern for Distributed Transactions

- Saga design pattern is provide to manage data consistency across microservices in distributed transaction cases.
- Saga offers to create a set of transactions that update microservices sequentially, and publish events to trigger the
  next transaction for the next microservices.
- If one of the step is failed, than saga patterns trigger to rollback transactions, do reverse operations with
  publishing rollback events to previous microservices.
- Publish/subscribe pattern with brokers or API composition.
- SAGA pattern manage long-running transactions that involve multiple microservices which is a series of local
  transactions that work together to achieve E2E use case.
- Useful in distributed systems, where multiple microservices need to coordinate their actions.
- Ensure that the overall transaction is either completed successfully, or is rolled back to its initial state. (
  compensating transaction)
- Saga pattern provides transaction management with using a sequence of local transactions of microservices. And
  grouping these local transactions and sequentially invoking one by one.
- Each local transaction updates the database and publishes an event to trigger the next local transaction. If one of
  the step is failed, than saga patterns trigger to rollback transactions.

### Types of Saga Implementation

There are two type of saga implementation ways:

- **Choreography-based** SAGA Implementation
- **Orchestration-based** SAGA Implementation

### Choreography-based SAGA Implementation

![img_1.png](pics/img_1678.png)

- Each microservice communicates with the other microservices by exchanging events using a message broker.
- Event-based approach allows for a more decentralized and flexible way to implement the SAGA pattern, as each
  microservice can publish to events to the others in real-time.
- Choreography provides to coordinate sagas with applying publish-subscribe principles.
- Each microservices run its own local transaction, publishes events to message broker system and trigger local
  transactions in other microservices.

Order Fullfilment with Choreography-based SAGA Implementation:

- The customer places an order on the e-commerce application and provides their payment information.
- The order microservice begins a local transaction and publishes an event to the message broker.
- The inventory microservice listens for the event published by the order microservice and, it begins a local
  transaction and reserves the items in the customer's order.
- The inventory microservice publishes an event to the message broker, the items were reserved successfully.
- The payment microservice listens for the event published by the inventory microservice and it charges the customer's
  payment method and commits its own local transaction.

Order Fullfilment Rollback with Choreography-based SAGA Implementation:

- SAGA pattern provides a way to roll back the changes made by each microservice.
- If the Inventory microservice encountered an error while reserving the items in the customer's order, it could publish
  a failure event to the message broker.
- Order fulfillment microservice would then execute a compensating transaction to undo the charges to the customer's
  payment method and cancel the order.

Benefits of Choreography-based SAGA:

- Decentralized and flexible By using an event-based approach, each microservice can react to events published with the
  others to coordinate their actions. This allows for a more decentralized and flexible approach to implementing the
  SAGA pattern.
- Decouple direct dependency of microservices when managing transactions. - Avoid Single Point of Failure Since there is
  no orchestrator, responsibilities are distributed across the saga participants.
- Simple workflows This way is good for simple workflows, if they don't require too much microservices transaction
  steps.

Drawbacks of Choreography-based SAGA:

- **More complex to manage** Each microservice communicates via events with the others, the choreography-based approach
  can be more complex to manage and may require more coordination among the microservices.
- SAGA workflow become confusing when adding new steps into flow.
- **Cyclic Event Consume Risk** There's a Cyclic Event Consume Risk dependency between saga participants because they
  have to consume each other's commands.
- **Result** Implementing the SAGA pattern will depend on the specific needs and constraints of the distributed system.
  It may be a good fit for some systems, but may not be suitable for others.
- Choreography-based implementation of SAGA is good for simple workflows if they don't require too much micorservices
  transaction steps.

### Orchestration-based SAGA Implementation

![img_2.png](pics/img_2fjgk.png)

- Orchestration-based SAGA pattern involves using a central orchestrator service to coordinate and manage the individual
  sagas or microservices that make up a transaction.
- The orchestrator is responsible for initiating the transaction and ensuring that each saga performs its step in the
  correct order.
- If any of the sagas fail to complete their step, the orchestrator can use the compensating transactions to roll back
  the changes and restore the system to its original state.
- Orchestration provides to coordinate sagas with a centralized controller microservice that orchestrate the saga
  workflow and invoke to execute local microservices transactions in sequentially.

Benefits and Drawback of Orchestration-based SAGA Implementation:

Benefits:

- Provides a clear and centralized point of control for managing transactions.
- Make it easier to understand and debug the system, and to add new transactions or modify existing ones.

Drawbacks:

- The orchestrator can also become a single point of failure, and if it goes down, the entire system may be unable to
  complete transactions.
- The orchestrator can become a bottleneck if the system is heavily loaded, as all transactions must go through it.

Result:

- It can be a useful approach for managing transactions in a distributed system, but it is important to carefully
  consider the trade-offs and potential drawbacks.
- Orchestration way is good for complex workflows which includes lots of steps.
- But this makes single point-of-failure with centralized controller microservices and need implementation of complex
  steps.

### Compensating Transaction Pattern

- Compensating Transaction pattern is a rollback process of SAGA Pattern.
- Compensating Transaction pattern is provides to reverse the steps for a previously executed transaction.
- In microservice architectures where multiple services may be involved in a distributed transaction, if any of the
  services fail to complete their part of the transaction, the effects of the entire transaction need to be undone.
- The steps in a compensating transaction should undo the effects of the steps in the original operation.
- Compensating transaction is also an eventually consistent operation and it could be fail.
- The system should be able to resume the compensating transaction at the point of failure and continue.
- The steps in a compensating transaction should be defined as **idempotent commands.**

### Dual Write Problem

- When application needs to change data in two different systems, i.e. a database and a message queue, if one of the
  writes fails, it can result in inconsistent data.
- Happens when you use a local transaction with each of the external systems operations.
- I.e. app needs to persist data in the database and send a message to Kafka for notifying other systems.
- If one of these two operations fails, the data will be inconsistent data and these two systems becomes inconsistent.

![img_3.png](pics/fsdgvdhbdgfbhrtgr.png)

How to avoid dual write problems in microservices ?

- **Monolith applications** use the 2 phase commit protocol.
- It splits the commit process of the transaction into 2 steps and ensures the ACID principles for all systems.
- Can't use 2-phase commit transactions when building microservices.
- These transactions require locks and don’t scale well.
- Need all systems to be up and running at the same time.

Solutions:

- **Transactional Outbox Pattern**
- **CDC - Change Data Capture**

Best Practice:

- Use Red Hat Apache Kafka and CDC using Debezium in event- driven applications.
- Use New databases like CockroachDB which has built-in Change Data Capture feature.

### Transactional Outbox Pattern

![img_4.png](pics/dsfgbdrrhwg.png)

- The idea is to have an “Outbox” table in the microservice’s database. It provides to publish events reliably.
- Dual write problem happens when application needs to change data in two different systems.
- Instead of sending the data to two separate locations, send a single transaction that will store two separate copies
  of the data on the database.
- One copy is stored in the relevant database table, and the other copy is stored in an outbox table that will publish
  to event bus.
- When API publishes event messages, it doesn’t directly send them, Instead, the messages are persisted in a database
  table.
- After that, a job publish events to message broker system in predefined time intervals.
- Events are not written directly to a event bus, it is written to a table in the "Outbox" role of the service.

- Transaction performed before the event and the event written to the outbox table are part of the same transaction.
- When a new order is added to the system, the process of adding the order and writing the Order_Created event to the
  Outbox table is done in the same transaction to ensure the event is saved to the database.
- If one of the process is fail, this will rollback the whole operations with following ACID principles.
- The second step is to receive these events written to the Outbox table by an independent service and write them to the
  Event bus. Another service listen and polls the Outbox table records and publish events.

- Microservice provides an outbox table within its database. Outbox table will include all the events.
- There will be a **CDC (change data capture) plugin** that reads the commit log of the outbox table and publish the
  events to the relevant queue.
- It provides that messages are reliably delivered from a microservice to another microservice even if the transaction
  that triggered the message fails.
- It involves storing the message in a local "Outbox" table within the microservice, that message sent to the consumer
  after the transaction is committed.
- Outbox pattern can be used to ensure that messages are delivered consistently, even if the microservice that sent the
  message is unavailable or experiencing errors.
- _Useful for communicating important information or updates between services._

Why Use Outbox Pattern ?

- When working with critical data that need to consistent and need to accurate to catch all requests.
- When the database update and sending of the message should be atomic to make sure data consistency.
- For example the order sale transactions, because they are about financial business. Thus, the calculations must be
  correct 100%.
- To access this accuracy, must be sure that our system is not losing any event messages.
- The Outbox Pattern should be applied this kind of cases.

### CDC - Change Data Capture with Outbox Pattern.

- Change Data Capture (CDC) is a technology that captures insert, update, and delete activity on a database.
- CDC typically works by continuously monitoring the transaction log of a database for changes, and then extracting and
  propagating those changes to the target system.
- This allows the target system to stay up-to-date with the source system in near real-time, instead of relying on
  batch- based data synchronization processes.
- CDC can be used in replicating data between databases, synchronizing data between systems in a microservices
  architecture, and enabling real-time data analytics.
- CDC is a way to track changes that happen to data in a database that captures insert, update, and delete activity and
  makes this information available to other systems.
- This allows those systems to stay up-to-date with the data in the database in real-time.

.:

- Outbox pattern is ensuring data changes made by a microservice are eventually propagated to other microservices.
- Whenever a microservice updates data in its database, it also writes a record to the outbox table with the details of
  the change.
- CDC can then be used to monitor the outbox table for new records, extract the data changes that propagated to the
  target microservices to be kept up-to-date with the data.
- Using CDC with the Outbox pattern allows microservices to decouple their data updates from the process of propagating
  those updates to other microservices.
- This can make it easier to scale and maintain a microservices architecture.
- Each microservice can focus on its own data updates and let CDC handle the synchronization of data between services.

![img_5.png](pics/xvcbzfgsdfg.png)

### CockroachDB for CDC and Outbox Pattern

- CockroachDB is a distributed database management system that is designed to be scalable, resilient, and easy to use.
- Cockroach cluster, which is a group of database nodes that work together to form a single, highly available database.
- Ability to scale horizontally by adding more nodes to the cluster as the workload increases.
- CockroachDB also has strong support for data consistency and durability, with features such as multi-active
  availability and distributed transactions.
- CockroachDB is written in the Go programming language.
- CockroachDB has built-in Change Data Capture feature, that you can build the Transactional Outbox Pattern with CDC
  into your own application.

### Azure Cosmos DB for CDC and Outbox Pattern

- Azure Cosmos DB is a globally distributed, multi-model database service offered by Microsoft Azure.
- It supports various database models, including document, key-value, column-family, and graph, and can be accessed
  through multiple APIs, such as SQL, MongoDB, Cassandra, and Azure Table Storage.
- Azure Cosmos DB has built-in support for Change Data Capture (CDC), allows it to track and propagate data changes made
  to the database to other systems in near real-time.
- CDC in Azure Cosmos DB works by continuously monitoring the transaction logs of the database for changes and
  extracting those changes to be sent to target systems.
- Azure Cosmos DB change feed API to access the data changes and process them in your application.

### Amazon DynamoDB Streams for CDC and Outbox

- DynamoDB supports streaming of item-level change data capture records in the near-real time.
- DynamoDB stream is an ordered flow of information about changes to items in a DynamoDB table.
- Whenever an application creates, updates, or deletes items in the table, DynamoDB Streams writes a stream record with
  the primary key attributes of the items that were modified.
- A stream record contains information about a data modification to a single item in a DynamoDB table. That can includes
  capture additional information, such as the “before” and “after” images of modified items.
- DynamoDB Streams writes stream records in near-real time so that you can build applications that consume these streams
  and take action based on the contents.
- Most of the applications can benefit from data capturing changes into DynamoDB table; Notifications, Mobile Apps,
  Financial Apps.

### Microservice Architecture with SAGA, Transactional Outbox and CDC Pattern

![img_6.png](pics/sfxvsdfgrefeg.png)

---

# Event-Driven Microservices Architecture

- Microservices architectures are designed to be highly modular and flexible, that can be scaled and managed separately
  and use of APIs to communicate between services.
- Event-driven architecture, microservices can communicate by publishing and subscribing to events, than directly
  calling each other's APIs.
- Event-driven microservice architecture is means communicating with microservices via event messages and we can do
  asynchronous behavior and loosely coupled.
- Instead of sending request when data needed, services consume them via events.
- Huge Innovations on the Event-Driven Microservices Architectures;
- Real-time messaging platforms, stream-processing, event hubs, real-time processing, batch processing, data
  intelligence.

![img.png](pics/sragsergrsteyerth.png)

- When a service needs to communicate with another service, it publishes an event to a message queue or event bus. Other
  services can then subscribe to that event and take appropriate action when the event is received.

- _Asynchronous communication :_ Allows services to communicate asynchronously. Service can publish an event and
  continue processing without waiting for a response from the other service.

- _Decoupled communication :_ Decouples the publisher and subscriber, allows to evolve independently without affecting
  each other.

- _Real-time processing:_ Support real-time processing, as events are published and consumed as soon as they occur. Need
  to react to events in real-time, such as in systems that use CDC to track changes to a database.

- _High volume events :_ Well-suited to handling high volume events, as they can scale horizontally by adding more event
  consumers as needed. Can be scaled independently to handle increased load.
- _Responsible business capability :_ Each service is responsible for a specific function or business capability.
- Services communicate with each other by publishing and subscribing to events, that make it easier to build and
  maintain complex systems.
- Allows to work on different parts of the system in parallel without having to worry about the impact on other
  components.

### Real-time Processing and High Volume Events in Event-Driven Microservices Architecture

- Real-time processing Real-time processing is achieved by using a message queue or event bus to publish and consume
  events as they occur.
- When an event is generated, it is published to the message queue or event bus and made available to any interested
  subscribers.
- Allows you to react to events in real-time, as they are published and consumed as soon as they occur.
- When need to perform real-time analytics or trigger actions based on changes to the data.
- High Volume Events Using a message queue or event bus that can handle high volumes of events and distribute them to
  multiple consumers.
- When you have a system that generates a large number of events, use a event bus to distribute those events to multiple
  consumers, that can process events in parallel.
- Allows you to scale up the number of event consumers as needed to handle increased load.
- Event-driven microservices architectures to process events in real-time and scale to handle high volumes of events.

### Event Hubs and Event Streaming in Event-Driven Microservices Architecture

An Event Hub is a specific infrastructure component designed for event ingestion and distribution, while an Event Stream
is a more general concept referring to the flow of events within an event-driven system. Event Streams may flow through
Event Hubs, but they can also come from other sources or be managed by different systems.

### Use Cases of Event Hubs and Event Streaming

- Use Case – Customer Purchase on E-Commerce E-commerce website that generates a large number of events as customers
  browse and purchase products.
- Using event streaming, build a real-time analytics pipeline that processes these events as they occur and generates
  insights in real-time.
- This could include tracking customer behavior, identifying trends and patterns, and triggering actions based on
  changes to the data.
- Use Case – Data Synchronization Different services may need to access the same data, and not be able to access each
  other's databases directly.
- Using event streaming, we can build a data synchronization pipeline that captures changes made to the data and streams
  them to other services in real-time. Ensures to access the most up-to-date data.
- Event Hubs and event streaming is a powerful tool for building real-time data pipelines in microservices architectures
  that provides flexible platform for data ingestion and distribution.

### Traditional Event-Driven Microservices Architecture

![img_1.png](pics/aserfasfsg.png)

### Evolved Event-Driven Microservices Architecture

![img_2.png](pics/sfdgsfgsdfh.png)

### Kafka Event-Driven Microservices Architecture

![img_3.png](pics/srafgsrgsdfg.png)

![img_4.png](pics/asfgasdfsrfgsrdfg.png)

---

# Microservices Distributed Caching

### What is Caching ?

- Caching for improving the performance of a system by storing frequently accessed data in a cache that can be quickly
  accessed from memory.
- Caching is to reduce the number of expensive operations, such as database queries or network requests.
- Caching can increase performance, scalability, and availability for microservices with reducing latency with cache and
  makes application faster.
- When the number of requests are increased, caching provide to handle requests with high availability.
- If application request mostly comes for reading data that is not changes so frequently, then Caching will be so
  efficient.
- I.e. reading product catalog from e-commerce application. Caching also provide to avoid re-calculation processes.
- By storing frequently accessed data in a cache, system can avoid the overhead of repeatedly expensive operations.

### Types of Caching

Types of Caching

- In-memory cache
  - Stores data in the main memory of a computer. In-memory caches are typically the fastest type of cache, but the data
    is lost when the cache is restarted or the machine is shut down.
- Disk cache
  - Stores data on a hard drive or solid-state drive. Disk caches are slower than in-memory caches, but they can persist
    data.
- Distributed cache
  - Cache is distributed across multiple machines and is typically used in distributed systems, such as microservices
    architectures.
  - Distributed caches can improve the performance and scalability of a system by allowing data to be stored and
    accessed from multiple locations.

### Distributed Caching in Microservices

- Distributed caching is improving the performance by storing frequently accessed data in a cache that can be quickly
  accessed from multiple locations.
- Microservices architectures are typically implement a distributed caching architecture: - Improve the performance of
  individual services by storing frequently accessed data locally.
- Reducing the need to make expensive calls to a database or other external system.
- How can we increase the speed of the microservices ? With using Distributed Cache.

- Microservices are responsible for a specific function and communicates with other services through well-defined
  interfaces, typically using APIs.
- By storing frequently accessed data locally in a cache, microservices can avoid the overhead of making repeated calls
  to an external system, resulting in faster response times.
- Benefits to using distributed caching in a microservices:
- Improved performance Services can avoid the overhead of making repeated calls to a database or other external system.
- Resilience Allowing services to continue functioning even if an external system becomes unavailable.
- Scalability Allowing services to handle increased traffic without the need to scale up the external system.

### Cache Hit and Cache Miss

- Cache hit occurs when the requested data can be found in the cache.
- Cache miss occurs when the requested data is not in the cache and must be retrieved from a slower storage db.
- Cache hits are desirable because they can improve the performance of a system by reducing the number of requests.
- Cache misses can have a negative impact on performance, because they require additional time and resources to retrieve
  the requested data.
- The cache hit rate is a measure of how often a cache is able to fulfill requests from its own store.
- High cache hit rate indicates that the cache is effective at storing frequently accessed data.
- Low cache hit rate may indicate that the cache is not large enough.

### Caching Strategies in Distributed Caching

There are several caching strategies that can be used in distributed microservices:

- Cache Aside
- Read-Through
- Write-Through
- Write-Back, Write-Behind

**Cache Aside:**

- Client checking the cache for data before making a request to the backend service. When microservices needs to read
  data from the database, it checks the cache first to determine whether the data is available.
- If the data is available (a cache hit), the cached data is returned. If the data isn’t available (a cache miss), the
  database is queried for the data.
- The client will retrieve the data from the backend service and store it in the cache for future requests.
- Data is lazy loaded into cache by client application.

**Read-Through Strategy:**

- When a client requests data that is not found in the cache, the cache will automatically retrieve the data from the
  underlying database and store it in the cache for future requests.
- Cache-aside strategy, when a client requests data that is not found in the cache, the client is responsible for
  retrieving the data from the database.
- Read-through cache strategy, when a client requests data that is not found in the cache, the cache will automatically
  retrieve the data from the database.

**Write-Through Strategy:**

- Update the cache whenever data is written to the backend service. Cache always has the most up-to-date data, but it
  can also result in a higher number of write operations.
- Instead of lazy-loading the data in the cache after a cache miss, the cache is proactively updated immediately
  following the primary database update.
- Data is first written to the cache and then to the database.
- In Write-Through, the data written to the cache is synchronously updated in the main database.

**Write-Back or Write-Behind:**

- In Write-Back or Write-Behind, the data written to the cache is asynchronously updated in the main database.

### Cache eviction

Cache eviction refers to the process of removing or evicting items from a cache to make room for new data or to manage
memory usage effectively. Caches have limited capacity, so when the cache is full and new items need to be added, the
cache must decide which existing items to remove.

There are several common cache eviction policies:

- Least Recently Used (LRU):
  - This policy removes the least recently accessed items from the cache when the cache is full. It assumes that items
    that have not been accessed recently are less likely to be accessed in the near future.

- Least Frequently Used (LFU):
  - This policy removes the least frequently accessed items from the cache when the cache is full. It assumes that items
    that have been accessed the least number of times are less likely to be accessed again.

- First-In-First-Out (FIFO):
  - This policy removes the oldest items that were added to the cache when the cache is full. It follows the principle
    of "first in, first out."

---

# Microservices Deployments

### Deployment Strategies for Microservices

- Blue-green deployment Deploying updates to a new set of microservices (the "green" deployment), while the old version
  of the microservices (the "blue" deployment) remains running.
- Rolling deployment Deploying updates to a subset of the microservices at a time, and then rolling the updates out to
  the rest of the microservices over time.
- Canary deployment Deploying updates to a small subset of the microservices, and then gradually rolling the updates out
  to the rest of the microservices over time.
- A/B testing Deploying updates to a subset of the microservices, and then comparing the performance of the updated
  microservices with the performance of the unmodified microservices.

---

# Microservices Resilience

### What is Microservices Resiliency ?

- Microservice should design for resiliency, needs to be resilient to failures and must accept partial failures.
- Design microservices to be resilient for partial failures, ability to recover from failures and continue to function.
- Accepting failures and responding to them without any downtime or data loss.
- Return the application to a fully functioning state after a failure.
- Assume that failures will happen and design our microservices for resiliency.
- Microservices are going to fail at some point, that's why we need to learn embracing failures.
- Microservices system is considered to be resilient, if it can continue to function effectively despite failures or
  disruptions.
- Should be fault tolerant and handle failures gracefully.

### Microservices Resiliency Patterns

- To provide unbroken microservice, the architecture must be designed correctly.
- We can apply to provide uninterrupted microservice, with “_Resilience Patterns_”.
- Resilience Patterns can be divided into different categories according to the problem area they solve.
- Ensuring the durability of services in microservice architecture is relatively difficult compared to a monolithic
  architecture.
- The communication between services is distributed and many internal or external network traffic is created for a
  transaction.
- Communication need between services and dependence on external services increases.
- The possibility of occurring errors will increase.
- There are several patterns that can be used to improve the resiliency of a microservices system.

**1. Retry Pattern**

Retrying a request if it fails or times out. Retries can be implemented at the client or the service level, to handle
temporary failures or disruptions.

- Inter-service communication can be performed by HTTP or gRPC and can be managed by developments.
- When in comes to network, server and such physical interruptions may be unavoidable.
- If one of the services returns HTTP 500 during the transaction, the transaction interrupted and an error may be
  returned.
- But when the user restarts the same transaction, it may work.
- More logical to repeat the request made to the 500 returned service, user will be able to perform the transaction
  successfully.
- Microservices communications can fail because of transient failures, but this failures happen in a short-time and can
  fix after that time.
- For that cases, we should implement Retry Pattern.
- Allow the microservice time to fix itself with self-correct, and extend the back-off time before retrying the call.
- The back-off period should be exponentially incremental withdrawal to allow sufficient correction time.

Drawbacks of Retry Pattern:

- The Retry pattern should be used with caution.
- If a service is experiencing persistent failures or is unavailable for an extended period of time, the retry pattern
  result in an excessive number of failed requests.
- In these cases, it is necessary to implement additional strategies, circuit breaking or fallback logic to prevent the
  retry pattern from further impacting the system.

**2. Circuit Breaker Pattern**

Introducing a proxy or "circuit breaker" between a client and a service. It will open the circuit and prevent further
requests from being sent to the service.

- Method in electronic circuits that is constructed like circuit breaker switchgear.
- Stop the load transfer in case of a failure in system to protect the electronic circuit.
- Protects against failures in external dependencies, useful in microservice when failure in one service can have
  cascading effects on other services.
- Circuit breaker pattern prevents cascading failures in a system.
- If one microservice depends on another microservice and that second microservice fails, the first microservice might
  also fail.
- If the first microservice is using a Circuit breaker pattern, it can prevent further calls to the second microservice
  and continue to function.
- .
- Microservices that allows users to book flights.
- This microservice depends on an external service for retrieving flight information.
- If the external service becomes unavailable, the flight booking microservice will also become unavailable.
- To protect against this type of failure, implement a circuit breaker pattern around the call to the external service.
- If the external service becomes unavailable, the circuit breaker will trip, preventing further calls to the external
  service.
- This will help to prevent cascading failures in the system
- .
- Circuit Breakers pattern monitors the communication between the services and follows the errors that occur in the
  communication.
- When the error in the system exceeds a certain threshold value, Circuit Breakers turn on and cut off communication,
  and returning previously determined error messages.
- While Circuit Breakers is open, it continues to monitor the communication traffic.
- If the requested service starts to return successful results, it becomes closed.

Circuit Breaker States:

- **Closed** The circuit breaker is not open and all requests are executed.
- **Open** The Circuit Breaker is open and it prevents the application from repeatedly trying to execute an operation
  while an error occurs.
- **Half-Open** The Circuit Breaker executes a few operations to identify if an error still occurs. If errors occur,
  then the circuit breaker will be opened, if not it will be closed.

**3. Bulkhead Pattern**

Partitioning a system into isolated components, or "bulkheads," to prevent the failure of one component from affecting
the others.

**4. Timeout Pattern**

Should not wait for a service response for an indefinite amount of time, throw an exception instead of waiting too long.

- Timeout Pattern provides that should not wait for a service response for an indefinite amount of time, throw an
  exception instead of waiting too long.
- It is used to handle scenarios where a service call takes longer than expected to complete.
- Setting a maximum time limit for the service call to complete, if the time limit is exceeded, the call is considered
  to have «timed out.»
- In microservices, It used to prevent service calls from taking too long to complete.
- If the payment processing service takes longer than expected to complete a request, the timeout pattern used to cancel
  the request and return an error to the shopping cart service.

**5. Fallback Pattern**

Providing an alternative behavior or response if a request fails or times out.

- Fallback Pattern provides an alternative behavior or response if a request fails or times out.
- If a service is unavailable, the client could use a cached version of the data or display a default message to the
  user.
- It is used to provide an alternative course of action when a service call fails or takes too long to complete.
- Define a fallback function that is called if the service call fails or times out, and the function provides an
  alternative response.
- In microservices, the fallback pattern help to improve the overall reliability and stability of the system.
- If the payment processing service is unavailable or takes too long to complete a request, the fallback pattern could
  be used to provide an alternative response.
- I.e. displaying an error message or offering the user the option to try again later.

--- 

# Microservices Observability with Distributed Logging and Distributed Tracing

- Microservice have a strategy for monitoring and managing the complex dependencies on microservices
- Need to implement microservices observability with using distributed logging and tracing features.
- Microservices Observability gives us greater operational insight.
- Monitor and understand the behavior and performance of a system made up of microservices.
- Distributed Logging and Distributed Tracing are two key tools that improve observability in microservices.
- Distributed Logging is a practice of collecting, storing, and analyzing log data from multiple service instances.
- Behavior of the system over time, identifying patterns and trends, and troubleshooting issues.
- Distributed Tracing is tracking the flow of requests through a microservices architecture, to see how the different
  service instances interact with each other.
- See the performance of the system, identifying bottlenecks, and troubleshooting issues.

### Elastic Stack for Microservices Observability with Distributed Logging

- Elastic Stack is a collection of open-source tools for collecting, storing, and analyzing log data and other types of
  data.
- Used for microservices observability to provides a flexible and scalable platform for monitoring and understanding the
  behavior.
- **Elasticsearch** Distributed search and analytics engine that can be used to store and index log data and other types
  of data.
- **Logstash** Data collection and transformation tool that used to collect log data from different sources and send it
  to Elasticsearch.
- **Kibana** Data visualization and exploration tool that used to create dashboards and visualizations based on data.
- **Beats** Collection of lightweight data shippers that used to collect log data and send it to Elasticsearch.

### Distributed Tracing with OpenTelemetry using Zipkin

- Distributed Tracing is used to track the flow of a request as it is processed by different microservices in a system.
- Different microservices are interacting and identify issues or bottlenecks and troubleshooting issues in the system.
- OpenTelemetry is an open-source project that provides a set of APIs for collecting and exporting telemetry data;
  traces, metrics, and logs.
- Zipkin is a distributed tracing system that collects and stores trace data from microservices, provides a web UI for
  viewing and analyzing trace data.
- To use OpenTelemetry with Zipkin for microservices distributed tracing, OpenTelemetry SDK would be integrated.
- Allows to collect trace data as requests flow through the system, and send data to a Zipkin server for storage and
  visualization.

### Microservices Health Checks: Liveness, Readiness and Performance Checks

- The process of monitoring the health and performance of individual microservices in a system.
- The failure of a single microservice can have cascading effects on the rest of the system, important to identify and
  address issues.
- What is Health Checks for microservices ?
- Health Checks for microservices are a way to monitor the health and performance of individual microservices in a
  system.
- Health checks used to determine whether a microservice is functioning properly and is able to handle requests.
- There are 3 types of health checks that can be used for microservices:

- Liveness Checks:
  - Determine whether a microservice is still running. If a liveness check fails, it may indicate that the microservice
    has crashed.
- Readiness Checks:
  - Determine whether a microservice is ready to handle requests. If a readiness check fails, it may indicate that the
    microservice is not yet ready to handle traffic.
- Performance Checks:
  - Monitor the performance of a microservice, such as response times or error rates. If the results of a performance
    check indicate that a microservice is not performing as expected.

### Microservices Health Monitoring with Kubernetes, Prometheus and Grafana

- Use Liveness and Readiness Probes
  - Kubernetes provides liveness and readiness probes that can be used to monitor the health of individual
    microservices.
- Liveness probes check to see if a microservice is still running, and readiness probes check to see if a microservice
  is ready to receive traffic.
- Use Monitoring Tools
  - Monitoring tools can be used to monitor the health and performance of microservices that can be integrated with
    Kubernetes to provide alerts or notifications when issues arise. I.e. Prometheus, Grafana, Datadog.
- Use Log Analysis Tools
  - Analyze log messages generated by your microservices and identify issues or trends. Elastic Stack (Elasticsearch,
    Logstash, Kibana), Fluentd, Splunk.
- Set up Alerts and Notifications
  - Setting up alerts and notifications can help to ensure that relevant parties are notified when issues arise,
    allowing them to be addressed quickly. Slack, Teams, Email, SMS.