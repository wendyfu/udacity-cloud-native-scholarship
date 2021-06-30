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
- a Docker image: [TBD]
- a Docker registry: [TBD]
