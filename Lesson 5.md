## LESSON 5: CI/CD with Cloud Native Tooling
In this lesson, we will explore how to use cloud-native tooling to construct a CI/CD pipeline.

![image](https://user-images.githubusercontent.com/13144571/126181528-420cf3ae-067a-456f-a45a-d5470f34ef5c.png)

- Below are the process that will be executed when new changes happened:
  - Build - compile the application source code and its dependencies. If this stage fails the developer should address it immediately as there might be missing dependencies or errors in the code.
  - Test - run a suite of tests, such as unit testing, integration, UI, smoke, or security tests. These tests aim to validate the behavior of the code. If this stage fails, then developers must correct it to prevent dysfunctional code from reaching the end-users.
  - Package - create an executable that contains the latest code and its dependencies. This is a runnable instance of the application that can be deployed to end-users.
  - Deploy - push the packaged application to one or more environments, such as sandbox, staging, and production. Usually, the sandbox and staging deployments are automatic, and the production deployment requires engineering validation and triggering.
- It is common practice to push an application through multiple environments before it reaches the end-users. Usually, these are categorized as follows:
  - Sandbox - development environment, where new changes can be tested with minimal risk.
  - Staging - an environment identical to production, and where a release can be simulated without affecting the end-user experience.
  - Production - customer-facing environment and any changes in this environment will affect the customer experience.

![image](https://user-images.githubusercontent.com/13144571/126182754-203c3b29-70b0-4ea3-8449-7bf908f8d923.png)

- Overall, a delivery pipeline consists of two phases:
  1. Continuous Integration (or CI) includes the build, test, and package stages.
  2. Continuous Delivery (or CD) handles the deploy stage.
