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

`docker exec -it <container_id>/<container_name> <process_name>`

> ***-it*** redirects the container's terminal (STDIN / STDOUT) to client's terminal. **-i** provides the facility to change the I/O of the container. **-t** gives a neat formattable terminal.

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

`docker exec` starts another process in an existing-running container. It is good for debugging and DB administration. But you cannot add ports and volumes, so on.


### Running an container
`docker run -name <container_name> -it  <image_name>:<optional_tag> <process>` 

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
` docker run -rm -it ubuntu bash -c "sleep 5; echo 'all done'`

`-rm` will remove the container soon the process assigned it completed.

## Dettached Containers
If you want to leave a container running.

`docker run -d -it ubuntu bash`

Now to access from any point/terminal:

`docker attach <container_name>`

Then `ctrl+p , ctrl+q` removes you out of container but container keeps on running.

## Container logs
We can see the logs for execution of the any process on the container basically output or the process.

`docker run -name test -it -d ubuntu bash -c "lose /etc/password'`

Here we can observe the container with name as *test* runs in detached mode. But the bash process will error out due to wrong command provided to it. To the logs we can use following commands:

`docker logs test`

```
Output:
bash: lose: command not found
```

>Note: If the output becomes too large then it might make the host system unresponsive. Therefore refrain logging the containers out put if the outputs are very large.

## Killing and Removing Containers

Killing container will bring the container to **STOPPED** state.

`docker kill <container_name>`

For removing/deleting the container

`docker rm <container_name>`

## Resource Constraints
We can configure resources like memory allocation, CPU shares.

`docker run --memory <maximum_allowed_memory> <image_name> <process>`

`docker run --cpu-shares <percentage share of CPU utilization as compared to other containers> <image_name> <process>`

`docker run --cpu-quota <maximum_percentage_share_for_CPU _utilisation> <image_name> <process>`

## Exposing docker ports

`docker run -p <host_port>:<docker's_port>/[tcp/udp] -p <host_port>:<docker's_port>/[tcp/udp] ... -name <container_name> <image_name> <process>`

If we dont provide the *host_port* it will be assigned atomatically. To see which ports have been assigned:

`docker port <container_name>`

Example:

```
[root@rohan_ilab_centos ~]# docker run -p 48736 -it -d --name centos76 centos bash
b463833944f4171db0552067b3de1ddc3d0319a669298c7a071d04d7e29371b2
[root@rohan_ilab_centos ~]# docker port centos76
48736/tcp -> 0.0.0.0:49156
48736/tcp -> :::49156
```

## Container Networking - Virtual Network

`docker network create <network_name>`

To add a container inside a virtual network;

`docker run -rm -it --net <networkname> --name <container_name> <image_name> <process>`

To add a container to multiple existing network:

`docker network connect <existing_network_name> <container_name>`

```
Example: Lets say we create two networks *school* and *class_10*. Now lets create two container:
docker run -it --net school --name teachers ubuntu bash     ---> teachers is inside school network
docker run -it --net class_10 --name 10A ubuntu bash          ---> 10A inside class_10 network
docker network connect class_10 teachers                    ----> teachers are also inside class_10 network

Now teachers container can ping to container present in both network, but 10A container can only ping to containers inside network class_10

```
## Legacy Linking
Docker provides another to link a container to other, but this connection is a one way cnnection.

`docker run -it --link <container_to_connect> --name <container_name > <image_name> <process>`

```
[root@rohan_ilab_centos ~]# docker run -it -d -e SECRET=xxxxxxx --name containerA centos bash
88b6b18df077ffc5d248087d307509aa6f38a2e797eee326802aa17aa597647e
[root@rohan_ilab_centos ~]# docker run -it --link containerA --name containerB centos bash
[root@0fc640e9dc97 /]# env
LANG=en_US.UTF-8
HOSTNAME=0fc640e9dc97
CONTAINERA_ENV_SECRET=xxxxxxx       ---------------> Recieved from container A
PWD=/
HOME=/root
CONTAINERA_NAME=/containerB/containerA      ---------------> Recieved from container A
TERM=xterm
SHLVL=1
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
LESSOPEN=||/usr/bin/lesspipe.sh %s
_=/usr/bin/env

containerB can connect to containerA and also access the environment variable set in containerA. This creates a security concern as the variables are exposed to other container who link with containerA.
But other way around is not true. Lets try pinging containerA from containerB

[root@0fc640e9dc97 /]# ping containerA 
PING containerA (172.17.0.2) 56(84) bytes of data.
64 bytes from containerA (172.17.0.2): icmp_seq=1 ttl=64 time=0.087 ms
64 bytes from containerA (172.17.0.2): icmp_seq=2 ttl=64 time=0.079 ms
64 bytes from containerA (172.17.0.2): icmp_seq=3 ttl=64 time=0.078 ms
64 bytes from containerA (172.17.0.2): icmp_seq=4 ttl=64 time=0.069 ms
^C
--- containerA ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3001ms
rtt min/avg/max/mdev = 0.069/0.078/0.087/0.008 ms

Now lets try pinging from containerA to containerB

[nroot@rohan_ilab_centos ~]$ docker attach containerA
[root@88b6b18df077 /]# ping containerB
ping: containerB: Name or service not known

Therefore B cannot be accessed from A.
```
## Vieing all the docker related information 
`docker info`

Example:
```
[nroot@rohan_ilab_centos Downloads]$ docker info
Client:
 Context:    default
 Debug Mode: false
 Plugins:
  app: Docker App (Docker Inc., v0.9.1-beta3)
  buildx: Build with BuildKit (Docker Inc., v0.5.1-docker)
  scan: Docker Scan (Docker Inc.)

Server:
 Containers: 55
  Running: 3
  Paused: 0
  Stopped: 52
 Images: 192
 Server Version: 20.10.6
 Storage Driver: overlay2
  Backing Filesystem: xfs
  Supports d_type: true
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Cgroup Version: 1
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 io.containerd.runtime.v1.linux runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: d71fcd7d8303cbf684402823e425e9dd2e99285d
 runc version: b9ee9c6314599f1b4a7f497e1f1f856fe433d3b7
 init version: de40ad0
 Security Options:
  seccomp
   Profile: default
 Kernel Version: 3.10.0-1062.9.1.el7.x86_64
 Operating System: CentOS Linux 7 (Core)
 OSType: linux
 Architecture: x86_64
 CPUs: 16
 Total Memory: 62.76GiB
 Name: rohan_ilab_centos
 ID: USMV:YA4G:G7G3:QC7J:HNWD:E6Q6:MDEP:3P5R:N6XF:YGMD:GU4B:PBSR
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false

```


## Removing images from host machine
Images do often consume lot of space on machine. To remove:

`docker rmi <image_name/id>`

## Docker Volumes
Volumes are sort of like shared folders, they're virtual discs that you can store data in and share them between the containers and between containers and the host, or both. So they have two main varieties of volumes, these virtual discs, available within Docker. You've got **persistent volumes**; you can put data there and it will be available on the host, and when the container goes away, the data will still be there. 

And you have **ephemeral volumes**; these exist as long as the container is using them, but when no container is using them, they evaporate. So they're sort of ephemeral, they'll stick around as long as they're being used, but they're not permanent.

### Sharing Folder between host and container

`docker run -it -v <path_to_host_folder>:<path_container_target_location> --name <container_name> <image_name> <process>`

Example:

```
[nroot@rohan_ilab_centos Downloads]$ docker run -it -v /data/home/nroot/Downloads:/Downloads --name containerC centos:latest bash
[root@f9dc99f56a2d /]# cd Downloads/
[root@f9dc99f56a2d Downloads]# ls
0BP_BANK_ATTR.txt
Account.txt
DateIssue2.txt
DateIssuelog.txt

```
### Sharing files between host and container

`docker run -it -v <path_to_host_file>:<path_container_target_file> --name <container_name> <image_name> <process>`

Make sure that the file exists before you start the container, or Docker will assume that it's going to be a folder and share it as a folder.

### Sharing data between containers

These are shared discs that exist only as long as they're being used, and when they're shared between containers, they'll be common for the containers that are using them.

Creating a shared Volume:

`docker run -it -v <path_container_shared_folder> --name <container_name> <image_name> <process>`

Attaching the shared volume:

`docker run -it --volumes-from <existing_container_name> --name <container_name> <image_name> <process>`

> If all the using containers are stopped the volume gets deleted.

## Docker Registry
Place where all docker images are stored. To secure the data and the images companies often create thier own registries.
### Finding docker images in a registry

`docker search <image_name>`

Example:

```
[nroot@rohan_ilab_centos Downloads]$ docker search centos
NAME                                         DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
centos                                       The official build of CentOS.                   7159      [OK]       
centos/systemd                               systemd enabled base container.                 108                  [OK]
centos/mysql-57-centos7                      MySQL 5.7 SQL database server                   94                   
centos/postgresql-96-centos7                 PostgreSQL is an advanced Object-Relational \u2026   45                   
kasmweb/centos-7-desktop                     CentOS 7 desktop for Kasm Workspaces            19                   
continuumio/centos5_gcc5_base                                                                3                    
kasmweb/core-centos-7                        CentOS 7 base image for Kasm Workspaces         3                    
couchbase/centos7-systemd                    centos7-systemd images with additional debug\u2026   1                    [OK]
spack/centos7                                CentOS 7 with Spack preinstalled                1                    
couchbase/centos-70-sdk-build                                                                0                    
couchbase/centos-72-java-sdk                                                                 0                    
fnndsc/centos-python3                        Source for a slim Centos-based Python3 image\u2026   0                    [OK]
bitnami/centos-extras-base                                                                   0                    
couchbase/centos-69-sdk-build                                                                0                    
couchbase/centos-72-jenkins-core                                                             0                    
spack/centos-stream                                                                          0                    
datadog/centos-i386                                                                          0                    
ibmcom/fhe-toolkit-centos-amd64              The IBM Fully Homomorphic Encryption (FHE) T\u2026   0                    
starlingx/stx-centos                         StarlingX centos                                0                    
ibmcom/fhe-toolkit-centos                    The IBM Fully Homomorphic Encryption (FHE) T\u2026   0                    
apache/couchdbci-centos                      Apache CouchDB CI CentOS                        0                    
silintl/openldap                             OpenLDAP base image on Centos 6                 0                    [OK]
bitnami/centos-base-buildpack                Centos base compilation image                   0                    [OK]
spack/centos6                                CentOS 6 with Spack preinstalled                0                    
couchbase/centos-69-sdk-nodevtoolset-build                                                   0                    
[nroot@rohan_ilab_centos Downloads]$ 
```

## What is Dockerfile?
Docker can build images automatically by reading the instructions from a Dockerfile. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image. Using docker build users can create an automated build that executes several command-line instructions in succession.

### To build an image using dockerfile:
`docker build -t <tag> .`   // Here tag is the tag which will be assigned to the images after build.

> Here "." resembles the path to where dockerfile resides. While processing of thr dockerfile, each step creates a new image.

Refer for information: https://docs.docker.com/engine/reference/builder/

While processing of thr dockerfile, each step creates a new image. Each step image is cached. EAch line does its own call to `docker run` and `docker commit`

### Dockerfile Syntax
#### FROM Statement
1. It defines which base image to be used.
2. Must be first command in the dockerfile.

`FROM <image_name>`

#### MAINTAINER Statement
1. It basically a comment which we add in the docker file to provide addtional information.

Example:
```
MAINTAINER Rohan Kumar @ron71
```
#### RUN Statement
1. It basically runs the commandline by creating a container, waits for it to finish and saves the result after committing it back in a new image.

Example:
```
RUN unzip install.zip /opt/install
RUN echo Hello Docker
```
#### ADD Statement
1. Basically it adds file from local system inside the image.

Example:
```
# Add a file
ADD run.sh /run.sh

# Add the content of the tar file -> it unzips the tar automatically at the target.
ADD project.tar.gz /install/

# Download content and add to image
ADD https://project.example.com/download/1.0/project.rpm /project/
```
#### ENV Statement
1. Set the environment variables for ***entire duration of the docker file***.

Example:
```
ENV DB_HOST=db.production.example.com
ENC DB_PORT=5432
```


