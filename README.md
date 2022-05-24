# Docker-Guide


## Virtualization
Refer: https://www.geeksforgeeks.org/virtualization-cloud-computing-types/

### BENEFITS OF VIRTUALIZATION 
1. More flexible and efficient allocation of resources. 
2. Enhance development productivity. 
3. It lowers the cost of IT infrastructure. 
4. Remote access and rapid scalability. 
5. High availability and disaster recovery. 
6. Pay peruse of the IT infrastructure on demand. 
7. Enables running multiple operating systems.

## Hypervisor
A hypervisor is a form of **virtualization software** used in Cloud hosting to divide and allocate the resources on various pieces of hardware. The program which provides partitioning, isolation or abstraction is called virtualization hypervisor. The hypervisor is a hardware virtualization technique that allows multiple guest operating systems (OS) to run on a single host system at the same time. A hypervisor is sometimes also called a virtual machine manager(VMM). 

Refer: https://www.geeksforgeeks.org/hypervisor/?ref=lbp


## What is Docker?
Docker is a open source project as a service products that use **OS-level virtualization** to deliver software in packages called containers.

## What is container?
According to Docker, a container is " a lightweight, stand-alone, executable package of a piece of software that includes everything needed to run it." The main purpose of Docker is that it lets you run microservice applications in a distributed architecture.

## Virtual Machines v/s Containers
![alt text](https://wiki.aquasec.com/download/attachments/2854029/docker-birthday-3-intro-to-docker-slides-18-638.jpg?version=1&modificationDate=1515522843003&api=v2)

1. **Virtual Machine:** Its a virtual server that emulates a hardware server. Your application will be depedndent on underlysing dependencies of other programs. This may run same on other virtual machine.
2. Having virtualization is not bad but the VM's OS consumes the hardware resorces a lot and even if the application doesnot has need of it.
3. **Containers** restrict he environment in which the application can run fine. Basically it bundels every dependency together along with the OS into a package.

## Docker Architecture
Docker's architecture is also client-server based. It consists of four main parts:

![alt text](https://wiki.aquasec.com/download/attachments/2854029/Docker.JPG?version=1&modificationDate=1515349366681&api=v2)


1. **Docker Client:** This is how you interact with your containers. Call it the user interface for Docker.
2. **Docker Objects**: These are your main components of Docker: *containers* and *images*. 
3. **Docker Daemon/Engine:** A background process responsible for receiving commands and passing them to the containers via command line. It helps to containerize the application and run. It communicates with underlying resources like Host OS and Hypervisor.
4. **Docker Registry:** Commonly known as Docker Hub, this is where your container images are stored and retrieved.

> Docker still needs virtualization (os-virtualization) as it contains Hypervison concept at root level. Thats why we need Hyper-V enabled in windows.

## Pulling Images
`docker pull <image_name>`

Example:

```
[root@rohan_ilab_centos ~]# docker pull busybox
Using default tag: latest
latest: Pulling from library/busybox
50e8d59317eb: Pull complete 
Digest: sha256:d2b53584f580310186df7a2055ce3ff83cc0df6caacf1e3489bff8cf5d0af5d8
Status: Downloaded newer image for busybox:latest
docker.io/library/busybox:latest
```

## Running a container
### Running an existing container
`docker start <container_id>/<container_name>`

`docker exec -it <container_id>/<container_name> <program_name>`

> ***-it*** redirects the container's terminal (STDIN / STDOUT) to client's terminal. **-i provides the facility to change the I/O of the container. **-t** gives a neat formattable terminal.

Example:
```
[root@rohan_ilab_centos ~]# docker start hardcore_ganguly
hardcore_ganguly
[root@rohan_ilab_centos ~]# docker ps
CONTAINER ID   IMAGE           COMMAND CREATED              STATUS       PORTS  NAMES
fe88c4305ef3   ubuntu:latest   "bash"  About an hour ago   Up 26 seconds        hardcore_ganguly
root@rohan_ilab_centos ~]# docker exec -it hardcore_ganguly bash
root@fe88c4305ef3:/# ls ------ inside the container
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@fe88c4305ef3:/# exit
exit
[root@rohan_ilab_centos ~]#
```

### Running an image
`docker run -it <image_name>:<optional_tag> <program>` 

Example:
```
[nroot@rohan_ilab_centos ~]$ docker run -it ubuntu:latest bash
root@cd5cc91a34f6:/# exit  ---- after exit the container goes in stopped state.
exit
[nroot@rohan_ilab_centos ~]$ docker ps -a
CONTAINER ID   IMAGE           COMMAND  CREATED              STATUS                         PORTS    NAMES
cd5cc91a34f6   ubuntu:latest   "bash"   About a minute ago   Exited (0) About a minute ago           determined_mccarthy
[root@rohan_ilab_centos ~]# docker start determined_mccarthy
determined_mccarthy
[root@rohan_ilab_centos ~]# docker ps
CONTAINER ID   IMAGE                                               COMMAND                  CREATED         STATUS          PORTS                                                           NAMES
cd5cc91a34f6   ubuntu:latest                                       "bash"                   6 minutes ago   Up 4 seconds                                                                    determined_mccarthy
fe88c4305ef3   ubuntu:latest                                       "bash"                   2 hours ago     Up 22 minutes                                                                   hardcore_ganguly
```