## LESSON 4: Open Source PaaS
In this lesson, we will explore the Platform as a Service (PaaS) mechanism as a way to deploy a service without underlying infrastructure knowledge.

- Managing Kubernetes at scale is challenging, especially when the clusters are self-hosted in datacenters or private clouds. In this case, a team has to keep up-to-date with the latest Kubernetes releases, to ensure the platform is updated, upgraded, managed, and configured to meet the production-grade standards. In these circumstances, if an organization does not have sufficient engineering resources, delegating the platform management to a 3rd party provider is a more suitable solution. This is covered by a PaaS or Platform as a Service solution.

![image](https://user-images.githubusercontent.com/13144571/126059718-1bdc9571-2cfc-4fb1-a886-bd4b139ad82f.png)

- Some of the widely used cloud-computing services are:
  - On-premise - where an engineering team has full control over the platform, including the physical servers
  - IaaS or Infrastructure as a Service - where a team consumes compute, network, and storage resources from a vendor. This service provides a suitable abstraction level for a team to build and run their own Kubernetes clusters. The most common IaaS solutions are delivered by public cloud providers such as AWS, GCP, Microsoft Azure, and many more.
  - PaaS or Platform as a Service - where the infrastructure is fully managed by a provider, and the team is focused on application deployment. Popular PaaS solutions are App Engine from GCP, Heroku, Cloud Foundry, Beanstalk from AWS, and many more. By default, the PaaS solutions offer the management of the underlying infrastructure, such as storage, databases, compute, hosting, and many more. Also, the majority of solutions will provide data analytics, security, and advanced scheduling.

![image](https://user-images.githubusercontent.com/13144571/126059688-ebbb6a4c-68f2-4426-99b4-2e49d88bbd5f.png)

-Cloud Foundry is an open-source PaaS, a stand-alone software package that can be installed on any available infrastructure; private, public, or hybrid cloud. Due to its open-source nature, there is no vendor lock-in and the community can contribute to its future releases and definition of the roadmap.
  - some offerings of Cloud Foundry, can be deployed on top of Kubernetes, meaning that its main components are running as pods within a cluster.
  - Cloud Foundry consists of multiple components that provide these core capabilities:
    - Routing - handle the incoming external traffic and route it to applications
    - Authentication - identity management to user accounts
    - Application lifecycle - controls the application deployments, monitors their status, and reconciles any new changes to reach the desired state of the application.
    - Application storage and execution - handle the availability of artifacts to applications
    - Service - use service brokers to provisions on-demand the dependency services for an application, such as a database or third-party APIs
    - Messaging - ensure the communication between Cloud Foundry components
    - Metrics and logging - aggregates the system and application metrics and logs

![image](https://user-images.githubusercontent.com/13144571/126060306-a03a54b9-a490-4208-9350-e2556fb34727.png)
- FaaS or Function as a Service - event-driven cloud-computing service that requires only the application code to execute successfully. FaaS is an event-driven cloud-computing service that allows the execution of code without any management of the infrastructure and configuration files.
- Popular FaaS solutions are AWS Lambda, Azure Functions, Cloud Functions from GCP, and many more.
