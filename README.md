# udacity-cloud-native-scholarship
Personal learning notes for [Udacity SUSE Cloud Native Foundations Scholarship](https://www.udacity.com/scholarships/suse-cloud-native-foundations-scholarship).

## LESSON 2: Architecture Consideration for Cloud Native Applications
- The two main approaches that are commonly referenced in the industry are **monoliths** and **microservices**. By the end of this lesson, we should be able to evaluate the most suitable architecture for you application considering the functional requirement, developer resources, and timeframe.
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
