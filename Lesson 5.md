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

- The result of CI represents a Docker image or an artifact that is OCI compliant. CI stages consist of:
  - Build: compiles the application source code and associated dependencies. Build stage occurs inside the Dockerfile.
  - Test: validate the behavior of application
  - Package: produce an executable that contains the latest features and their dependencies. This stage is implemented using `docker build` and `docker push` commands.

- There are plenty of tools that automate the Continuous Integration stages, such as Jenkins, CircleCI, Concourse, and Spinnaker.
- GitHub Actions are event-driven workflows that can be executed when a new commit is available, on external or scheduled events.
- For CI, GitHub actions are used to build and test the code. These GitHub actions can be easily integrated within any repository and provide immediate response whenever a commit passes the quality check. GitHub actions are supported for multiple programming languages and can offer tailored notifications (e.g. in Slack) and status badges for a project (such as whenever a workflow has passed or failed).
- ![image](https://user-images.githubusercontent.com/13144571/126334718-e78dc278-9368-4e3e-b95f-eda90d91d6f9.png)
- A GitHub action consists of one or more jobs. A job contains a sequence of steps that execute standalone commands, known as actions. When an event occurs, the GitHub Action is triggered and executes the sequence of commands to perform an operation, such as code build or test.
- GitHub Actions are configured using YAML syntax, hence uses the .yaml or .yml file extensions. These files are stored in .github/workflows directory within the code repository.

- The ecosystem is rich in the collection of tools that automate the Continuous Delivery stage, such as Jenkins, CircleCI, Concourse, and Spinnaker. However, in this lesson, we will explore ArgoCD to propagate an application to multiple Kubernetes clusters.
- ArgoCD operates on configuration stored in manifests (declarative) and uses Git repositories as the source of truth for the desired state of an application (GitOps pattern).
- Additionally, ArgoCD offers deployment to target environments and multiple clusters, and support for multiple config management tools (such as plain YAML, Helm, Kustomize).
- If you'd like to manage the manifest for multiple microservices, ArgoCD provides the App of Apps technique. For example, it can use an Web-store application to manage the application CRDs for the UI, Login, and Payment components.  You'll have one endpoint to control the release and management of multiple microservices.
  - ![image](https://user-images.githubusercontent.com/13144571/126448518-9de4ab6c-837f-437b-852a-9ccb2993c881.png)

- Configuration Manager
  - A CI/CD pipeline is used to push the code from multiple environments. These clusters are similarly configured and the deployment of the application for these environments is consistent. To reduce the management overhead of overseeing a similar configuration for each cluster, templating is introduced.
    ![image](https://user-images.githubusercontent.com/13144571/126590828-e13eca83-c6bd-4ffc-a4f0-c9083ff0b558.png)
  - For example, to deploy the nginx-alpine application 4 resource manifests were necessary: Namespace, Deployment, Service, and Configmap. However, this suite of manifests represents the deployment to a single environment, e.g sandbox. The application should be propagated through staging and production environment, which reference a separate set of manifests. It is essential to ensure that the application configuration is tailored for each environment, for example, allocate more CPU and memory to the application in production since it handles more traffic, or have a different number of replicas for each cluster. In this case, an engineering team ends up managing 3 sets of manifests, 1 for each cluster.
    ![image](https://user-images.githubusercontent.com/13144571/126590976-b19f964a-211c-4677-ae6d-609fe102ba4a.png)
  - However, the number of manifests grows exponentially when the application is distributed across multiple regions. As such, if the application is released in AP(Asia Pacific) and the US, a team ends up managing 9 different sets of manifests.
  - It is clear that it is necessary to introduce a mechanism to store and manage manifests in a reliable, scalable, and flexible way. This capability is offered by configuration management tools, such as:
    - Helm - package manager that templates exiting manifests, and uses input files to tailor configuration for each environment
    - Kustomize - a template-free mechanism that uses a base and multiple overlays, to manage the configuration for each environment
    - Jsonnet - a programming language, that enables the templating of manifests as JSON files, that can be easily consumed by Kubernetes

- **Helm** is a package manager, that manages Kubernetes manifests through charts. A Helm chart is a collection of YAML files that describe the state of multiple Kubernetes resources. These files can be parametrized using Go template.
    ![image](https://user-images.githubusercontent.com/13144571/126591122-725a6bba-2859-4bf2-9bd3-111c76493453.png)
  - A Helm chart is composed of the following files:
    - Chart.yaml - expose chart details, such as description, version, and dependencies
    - templates/ folder - contains templates YAML manifests for Kubernetes resources
    - values.yaml - default input configuration file for the chart. If no other values file is supplied, the parameters in this file will be used.
