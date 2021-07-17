## LESSON 3: Container Orchestration with Kubernetes
![Lesson outline](https://user-images.githubusercontent.com/13144571/123743251-224d6d80-d8d7-11eb-8588-ec20860ebd9e.png)

- Transitions from VMs to Containers
    ![image](https://user-images.githubusercontent.com/13144571/123744260-b835c800-d8d8-11eb-84dc-827178fc4b8c.png)
  - A VM is composed of an OS with a set of pre-installed libraries and packages.
  - A set of VMs is managed through a **hypervisor**. Hypervisor provides the _virtualization of the **infrastructure**_ and manages multiple VMs on the available servers.
  - Downside: it is not efficient enough. For example: to run app A, B, and C, you replicate the same OS 3 times. Having a full copy of an OS takes gigabytes. The more apps you run, the more space you would allocate to replication of the OS alone.
  - Improvement: new virtualization technique - _virtualization of the **OS**_, which facilitated the appearance of **containers**. A container contains the bare minimum to execute the application (e.g., code, config files, and dependencies).
  - Instead of having multiple VMs, we can have one hosting OS that trans multiple containers. The processes in the containers are completely isolated but are able to access the OS filesystem, resources, and packages.
  - The creation and execution of containers is delegated to a container management tool, such as Docker.
  - OS-level virtualization unlock the possibilty to have multiple applications on the same OS by using containers.
- Docker for Application Packaging: In the containerized world, source code + config files + dependencies will produce an artifact that can be executed in container. Multiple containers will be managed by Docker (container manager).
- When operating an application of Docker, there are three main objects required: a Dockerfile, a Docker image, and a Docker Registry.
- a Dockerfile: a set of instructions that would create a Docker image. Each instruction is an operation used to package an application, such as installing dependencies, compile the code, or impersonate a specific user. Each operation will represent a layer in the Docker image. The layers are cached and if the Dockerfile is modified, only the layers that are changed will be rebuild, resulting in lightweight and quick process for building a Docker image.
  - a Docker file is constructed using predefined instructions which contain step by step guide to build a Docker image and run the container successfully. Here's a list of some of the widely used instructions:
    | Syntax | Description |
    | ----------- | ----------- |
    | FROM | to initialize the build and set the base image |
    | RUN    | to execute a `command` |
    | COPY & ADD | to copy files from host to the container |
    | CMD | to set the default command to execute when the container starts |
    | EXPOSE | to expose an application port |
        
    Below is an example of a Dockerfile that targets to package a Python hello-world application:
```
# set the base image. Since we're running 
# a Python application a Python base image is used
FROM python:3.8
# set a key-value label for the Docker image
LABEL maintainer="Katie Gamanji"
# copy files from the host to the container filesystem. 
# For example, all the files in the current directory
# to the  `/app` directory in the container
COPY . /app
#  defines the working directory within the container
WORKDIR /app
# run commands within the container. 
# For example, invoke a pip command 
# to install dependencies defined in the requirements.txt file. 
RUN pip install -r requirements.txt
# provide a command to run on container start. 
# For example, start the `app.py` application.
CMD [ "python", "app.py" ]
```
- a Docker image: the DOckerfile instructionns will be used to build a Docker image. A Docker image is a read-only template that is used to spin up a runnable instance of an application. In a nutsell, we can use a Docker image to create a container with the package source code, configuration files, and any dependencies related to the application.
  - A Docker image can be built from an existing Dockerfile using the `docker build` command. Below is the syntax for this command:
```
# build an image
# OPTIONS - optional;  define extra configuration
# PATH - required;  sets the location of the Dockefile and  any referenced files 
docker build [OPTIONS] PATH

# Where OPTIONS can be:
-t, --tag - set the name and tag of the image
-f, --file - set the name of the Dockerfile
--build-arg - set build-time variables

# Find all valid options for this command 
docker build --help
```
  - For example, to build the image of the Python hello-world application from the Dockerfile, the following command can be used:
```
# build an image using the Dockerfile from the current directory
docker build -t python-helloworld .

# build an image using the Dockerfile from the `lesson1/python-app` directory
docker build -t python-helloworld lesson1/python-app
```
  - To create a container using an available Docker image, the docker run command is available. Below is the syntax for this command:
```
# execute an image
# OPTIONS - optional;  define extra configuration
# IMAGE -  required; provides the name of the image to be executed
# COMMAND and ARGS - optional; instruct the container to run specific commands when it starts 
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

# Where OPTIONS can be:
-d, --detach - run in the background 
-p, --publish - expose container port to host
-it - start an interactive shell

# Find all valid options for this command 
docker run --help
```
  - For example, to run the Python hello-world application, using the created image, the following command can be used:
```
# run the `python-helloworld` image, in detached mode and expose it on port `5111`
docker run -d -p 5111:5000 python-helloworld
```
(Note: To access the application in a browser, we need to bind the Docker container port to a port on the host or local machine. In this case, 5111 is the host port that we use to access the application e.g. http://127.0.0.1:5111/. The 5000 is the container port that the application is listening to for incoming requests.)
  - To retrieve the Docker container logs use the docker logs {{ CONTAINER_ID }} command. For example:
```
docker logs 95173091eb5e

## Example output from a Flask application
 * Serving Flask app "app" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
```
- a Docker registry: Once we have created and tested the Docker image, the next step is to store it to an image registry. This enables us to access the image on other machines than our local desktop. As such, an image registry is a mechanism to store and distribute Docker images.
  - Public Docker image registry example: DockerHub, Harbor, Google Container Registry.
  - Private image registry enables image restriction to be private and only available to trusted parties (a team would have full control on who can access and build the image).
  - Step for distributing Docker image:
    1. tag the image: use `docker tag`. If no tag provided, then the image will be automatically allocated a random alphanumeric string ID.
    2. push the image: use `docker push`
  - Example:
```
# tag an image
# SOURCE_IMAGE[:TAG]  - required and the tag is optional; define the name of an image on the current machine 
# TARGET_IMAGE[:TAG] -  required and the tag is optional; define the repository, name, and version of an image
# docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

# tag the `python-helloworld` image, to be pushed 
# in the `pixelpotato` repository, with the `python-helloworld` image name
# and version `v1.0.0`
docker tag python-helloworld pixelpotato/python-helloworld:v1.0.0
```

```
# push an image to a registry 
# NAME[:TAG] - required and the tag is optional; name, set the image name to be pushed to the registry
# docker push NAME[:TAG]

# push the `python-helloworld` application in version v1.0.0 
# to the `pixelpotato` repository in DockerHub
docker push pixelpotato/python-helloworld:v1.0.0
```
- The Container Orchestrator Framework
  - Framework that has capability to create, manage, configure thousands of containers on a set of distributed servers while preserving the connectivity and reachability of these containers.
  - Example: Docker Swarm, Apache Mesos, CoreOS Fleet and **Kubernetes**. Kubernetes took the lead in defining the principles of how to run containerized workloads on a distributed amount of machines.
  - Kubernetes is a graduated CNCF project and is widely adopted in the industry today. Kubernetes solutionizes:
    - Portability: highly portable, open-source nature leading to vendor agnosticism, can be hosted on any available infrastructure (including private, public, and hybrid cloud).
    - Scalability: has built-in resources such as Horizontal Pod Autoscaler (HPA), to determine the required amount of replicas for a service. Elasticity is a core feature that is highly automated within Kubernetes.
    - Resilience: it is important to be able to recover from failure fast and build a set of playbooks that minimizes the downtime of an application. Kubernetes has ReplicaSet, readiness, and liveness probes to handle most of the container failures, which enable powerful self-healing capability.
    - Service Discovery: provides cluster level DNS which simplifies the accessibility of workloads within the cluster. Also provides routing and load balancing of incoming traffic, ensuring that all requests are served without application overload.
    - Extensibility: uses building-block principle - has a set of basic resources that can be easily adjusted. Also provides a rich API interface (Custom Resource Definitions - CRD) that can be extended to accomodate new resources.
    - Operational Cost: efficiency of resource consumption within a Kubernetes cluster, such as CPU and memory.
- Kubernetes Architecture:
    ![image](https://user-images.githubusercontent.com/13144571/124419576-f4a96e00-dd87-11eb-9ee4-ab25ce935b31.png)
  - Kubernetes cluster is composed of a collection of nodes. Nodes are categorized into: master and worker nodes. Nodes could be distributed physical or virtual servers. The components installed on a node, determine the functionality of a node and identifies it as a master or worker node. The collection of master nodes represents the control plane, while the collection of worker nodes represents the data plane.
  - The Control Plane: concists of components that make global decisions about the cluster.
    - ![image](https://user-images.githubusercontent.com/13144571/124420384-97162100-dd89-11eb-9698-c6d1eafbca4f.png)
    - Control plane components are:
      - **kube-apiserver** - the nucleus of the cluster that exposes the Kubernetes API, and handles and triggers any operations within the cluster
      - **kube-scheduler** - the mechanism that places the new workloads on a node with sufficient satisfactory resource requirements
      - **kube-controller-manager** - the component that handles controller processes. It ensures that the desired configuration is propagated to resources
      - **etcd** - the key-value store, used for backs-up and keeping manifests for the entire cluster
  - There are two additional components on the control plane, they are **kubelet** and **k-proxy**. These two are special and important as they are installed on all node. You can see the Data Plane below for more details.
  - Data Plane: consists of the compute used to host workloads
    - ![image](https://user-images.githubusercontent.com/13144571/124420567-e2303400-dd89-11eb-8b6a-d1ccd7ea4cd3.png)
    - Data plane components are:
      - **kubelet** - the agent that runs on every node and notifies the kube- apiserver that this node is part of the cluster
      - **kube-proxy** - a network proxy that ensures the reachability and accessibility of workloads places on this specific node
    - Important Note: The kubelet and kube-proxy components are installed on all the nodes in the cluster (master and worker nodes). These components keep the kube-apiserver up-to-date with a list of nodes in the cluster and manages the connectivity and reachability of the workloads.
- To deploy Kubernetes cluster, you need to make sure that the control plane and data plane components are up and running. This process is referred as bootstrapping of a cluster. It is possible to manually bootstrap a cluster, but it is not recommended since it is a highly tedious task that has a higher risk of misconfiguration.
- To access a Kubernetes cluster, a **kubeconfig** file is required. A kubeconfig file has all the necessary cluster metadata and authentication details, that grants the user permission to query the cluster objects.
  - Usually, the kubeconfig file is stored locally under the `~/.kube/config` file. However, k3s places the kubeconfig file within `/etc/rancher/k3s/k3s.yaml` path.
  - Additionally, the location of a kubeconfig file can be set through the --kubeconfig kubectl flag or via the KUBECONFIG environmental variable.
- A kubeconfig file has 3 main distinct sections:
  1. Cluster: encapsulates the metadata for a cluster, such as the name of the cluster, API server endpoint, and certificate authority used to check the identity of the user.
  2. User: contains the user details that want access to the cluster, including the user name, and any authentication metadata, such as username, password, token or client, and key certificates.
  3. Context: links a user to a cluster. If the user credentials are valid and the cluster is up, access to resources is granted. Also, a `current-context` can be specified, which instructs which context (cluster and user) should be used to query the cluster.
Example of a kubeconfig file:
```
apiVersion: v1
# define the cluster metadata 
clusters:
- cluster:
    certificate-authority-data: {{ CA }}
    server: https://127.0.0.1:63668
  name: udacity-cluster
# define the user details 
users:
# `udacity-user` user authenticates using client and key certificates 
- name: udacity-user
  user:
    client-certificate-data: {{ CERT }}
    client-key-data: {{ KEY }}
# `green-user` user authenticates using a token
- name: green-user
  user:
    token: {{ TOKEN }}
# define the contexts 
contexts:
- context:
    cluster: udacity-cluster
    user: udacity-user
  name: udacity-context
# set the current context
current-context: udacity-context
```
- Kubernetes Resources:
  - Pod - smallest manageable unit within a cluster that provides the execution environment for an application
  - ReplicaSet - a mechanism to ensure a number of pod replicas are up and running at all times
  - Deployment - describe the desired state of the application to be deployed
- Rolling out Strategies:
![image](https://user-images.githubusercontent.com/13144571/126027187-9398276c-8a95-4793-bb5b-ba1197cdebcf.png)
1. The Go hello-world application is running version v1.0.0 in a pod managed by a ReplicaSet
2. The version of Go hello-world application is set to v2.0.0
3. A new ReplicaSet is created that controls a new pod with the application running in version v2.0.0
4. The traffic is directed to the pod running v2.0.0 and the pod with the old configuration (v1.0.0) is removed
- Services
  - Within a cluster, every pod is allocated 1 unique IP which ensures connectivity and reachability to the application inside the pod. For example, we can connect a workload within the cluster to access a pod directly via its IP. However, if the pod dies, all future requests will fail, as these are routes to an application that is not running. The remediation step is to configure the workload to communicate with a different pod IP. This is a highly manual process, which brings complexity to the accessibility of an application. To automate the reachability to pods, a Service resource is necessary.
  - ![image](https://user-images.githubusercontent.com/13144571/126027298-93ab617f-8031-4fe6-87e5-4a1fae58ce4d.png)
  - A Service resource provides an abstraction layer over a collection of pods running an application. A Service is allocated a cluster IP, that can be used to transfer the traffic to any available pods for an application. As such, as shown in the above image, instead of accessing each pod independently, the workload (1) should access the service IP (2), which routes the requests to available pods (3).
  - There are 3 widely used Service types:
    - ClusterIP - exposes the service using an internal cluster IP. If no service type is specified, a ClusterIP service is created by default.
    - NodePort - expose the service using a port exposed on all nodes in the cluster.
    - LoadBalancer - exposes the service through a load balancer from a public cloud provider such as AWS, Azure, or GCP. This will allow the external traffic to reach the services within the cluster securely.
- Ingress resources enabling access from the external users to services within the cluster. An Ingress exposes HTTP and HTTPS routes to services within the cluster, using a load balancer provisioned by a cloud provider. Additionally, an Ingress resource has a set of rules that are used to map HTTP(S) endpoints to services running in the cluster. To keep the Ingress rules and load balancer up-to-date an **Ingress Controller** is introduced.
  - ![image](https://user-images.githubusercontent.com/13144571/126027329-66790202-437d-4743-ab3a-1ef59be4137c.png)
  - For example, as shown in the image above, the customers will access the go-helloworld.com/hi HTTP route (1), which is managed by an Ingress (2). The Ingress Controller (3) examines the configured routes and directs the traffic to a LoadBalancer (4). And finally, the LoadBalancer directs the requests to the pods using a dedicated port (5).
