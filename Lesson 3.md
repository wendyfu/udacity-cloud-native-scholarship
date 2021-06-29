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
