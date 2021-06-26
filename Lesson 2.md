# udacity-cloud-native-scholarship
Personal learning notes for [Udacity SUSE Cloud Native Foundations Scholarship](https://www.udacity.com/scholarships/suse-cloud-native-foundations-scholarship).

## LESSON 2: Architecture Consideration for Cloud Native Applications
- The two main approaches that are commonly referenced in the industry are **monoliths** and **microservices**. By the end of this lesson, we should be able to evaluate the most suitable architecture for your application considering the functional requirement, developer resources, and timeframe.
- When building an application, do context discovery:
  1. List the **functional requirements**: what application capabilities should deliver to the end-users. Find out the following:
      - Stakeholders: the personas that require and sponsor this application, e.g., marketing teams want to build a new dashboard.
      - Functionalities: list the features, e.g., dashboard should include chat feature.
      - End users: the application's user: internal users or customer-facing app.
      - Input and output process: example, should output notification or should require customer details?
      - Engineering teams: which teams has the skill and time frame to implement the project.
  3. Enumerate the **available resources**, what can facilitate or block the product release.
      - Engineering resources: number of engineers / ability to hire contractors.
      - Financial resources: how much can the business spend to ensure a successful release of the product.
      - Timeframes: any urgency to reach the market.
      - Internal knowledge: skill / tools to facilitate the development of the application.
- Regardless of the adopted design, whether is monoliths or microservices, the main goal is to design an application that delivers value to customers and can be easily adjusted by the engineering team to extend the existing functionality.
- At a macro level, an application is composed of three main tiers:
  1. UI: responsible for handling HTTP requests triggered by the users and return a response.
  2. Business Logic: the main component which represents a collection of functionalities that provide a service to the customers.
  3. Data Layer: accessing the storing objects through the application execution, e.g., database
- In monolithic approach:
  - all three tiers are part of the same unit
  - all business logic are developed and managed in a single repository
  - all business logic are sharing resources (CPU and memory)
  - all business logic are using same programming language
  - packaging, distribution, and deployment are represented by single artifact or binary (which will include the code for the entire stack)
- Example of a Booking Application using Monolithic approach:
  - ![booking_app_monolith](https://user-images.githubusercontent.com/13144571/122892774-e1ea6e80-d36f-11eb-9129-73ce016f3205.png)
- Microservices approach:
  - aims to breakdown the application into smaller, independent units
  - each functionality represent a separate service containing its logic
  - each functionality represent a separate service containing its own allocated resource for CPU and memory
  - each services exposes an API for communication and interaction with other available units
  - every unit is written in the programming language of choices (enable concurrent development because multiple services can be built at the same time)
  - packaging, distribution, and deployment for each services is composed of a separate repository with its own binary (contains code and dependecies for that unit alone)
- Example of a Booking Application using Microservices approach:
  - ![booking_app_microservices](https://user-images.githubusercontent.com/13144571/122896565-61c60800-d373-11eb-98f7-2bfe16196e05.png)
- Choosing an architecture for an application is highly impacted by the function requirement and the available resources: if the organization has unlimited resources and powerful engineering force, the a microservice approach can be easily adopted. However, if a team is considering to build a new application, but only has few engineers available and a tight time frame, then a monolithic structure will help them hit the ground quicker.
- Further reading:
  - [What’s the Difference Between Monolith and Microservices?](https://nordicapis.com/whats-the-difference-between-monolith-and-microservices/)
  - [Microservices vs Monolithic Architecture](https://www.mulesoft.com/resources/api/microservices-vs-monolithic)
- The trade-offs for Monoliths and Microservices:
  - **Development Complexity**: addresses the effort required to deploy and manage an application.
  - **Scalability**: captures how an application scales up and down, based on the incoming traffic.
  - **Time to deploy**: refers to the ability to build a delivery pipeline and ship features.
  - **Flexibility**: implies the ability to adapt to new technologies and introduce new functionalities.
  - **Operational Cost**: encapsulates the necessary resources to build, deploy, and release a product.
  - **Reliability**: ability to recover from failure and waste monitoring application.
  - Table comparison:
    | Trade-offs | Monoliths | Microservices |
    | --- | --- | --- |
    | Development Complexity | Single framework/language, single repository, sequential development | Multiple languages, multiple repository (separated codebase), enables concurrent development |
    | Scalability | Replication of the entire stack with all the functionalities causing more space consumption than actually needed | Replication of separate functionalities (only replicate specific component alone), on-demand resource consumption |
    | Time to deploy | - One delivery pipeline (all components are developed in the same code repository)<br />- Entire stack deployment - can be disastrous if a release failes as it will take down the entire application (high risk of violating the zero downtime principle)<br />- Low velocity at scale as every release implies the redeploy of the entire stack | - Multiple delivery pipeline (one pipeline per codebase)<br />- Separate function deployment (each function is deployed independently without affecting the availability for the component - less risk to take down the entire application within release)<br />- Allow increase velocity of future development as we can have more releases with less risk |
    | Flexibility | Low - need to change entire stack | High - loosely coupled and allows independent changes to services |
    | Operational Cost | Low initial cost - only one codebase so there's only one delivery pipeline, but **high** cost at scale - more complexity and consume more resources when replicated | High initial cost - multiple delivery pipelines, but **low** cost at scale - proportional to the required resources at the time |
    | Reliability | Need to troubleshoot and recover the **entire** stack<br />- Low visibility - difficult to have granular visibility because the entire application will be aggregated together | - Recovery of the **failed component** only<br />- High visibility - can have detailed metrics and logs of a separate unit |
- Each application architecture has a set of trade-offs that need to be considered at the **genesis** of a project. But more importantly, it is paramount to understand how the application will be **maintained in the future** e.g. at scale, under load, supporting multiple releases a day, etc.
- A set of good development practices can be applied to improve the application lifecycle throughout the release and maintenance phases, regardless of the architecture chosen. These practices are focused on:
  - Health checks
    - Implemented to showcase the status of an application.
    - Usually represented by an HTTP endpoint such as `/healthz` or `/status` that return an HTTP response showcasing if the application is healthy or in an error state.
  - Metrics
    - Are necessary to understand the behavior of an application, contains statistics for individual service. These statistics should be gathered for individual services as it will increase the visibility of what resources the application requires to be fully operational (e.g., amount of CPU, memory, or network throughput).
    - Also can report the number of resources it is capable to handle, e.g., amount of request, active users, or the amount of logins.
    - Usually, the metrics are consumed via an HTTP endpoint such as forward `metrics` which returns the statistics for the service, e.g., the amount of users or the amount of active users.
  - Logs
    - The heart of any troubleshooting and debugging process for a project, logs are used to record operations that are performed by a service, e.g., whether a user has successfully logged into a service or a user encountered an error while reaching a particular function
    - Logs are collected through a passive logging mechanism, via STDOUT (standard out) and STDERR (standard error). This means that any output or errors from the application are sent to the shell, then collected by a logging tool, such as Fluentd or Splunk, and stored in back-end storage or database.
    - However, the application can send the logs record directly to the back-end storage or a database. In this case, an active logging is used, as the log transmission is handled directly by the application, without a logging tool required.
  - Tracing
    - Showing a holistic picture of how different services are invoked to fulfill a request.
    - Make it possible to recreate and analyze the lifecycle for request and identify key metrics within an application.
    - Tracing is implemented at the application layer, where the developer will be able to record when a function is invoked.
  - Resource conusumption
    - Refers to the amount of CPU and memory an application requires to be fully operational.
    - Additionally, it is beneficial to benchmark the network throughput, or how many requests can an application handle concurrently.
    - Having awareness of the application boundaries is essential to ensure 24/7 service availabilty.
