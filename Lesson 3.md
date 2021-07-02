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
