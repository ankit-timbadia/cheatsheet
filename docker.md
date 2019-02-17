
#### What is Docker
```
Docker is a Container runtime, there are number of other Container like Lxc / Rkt / containerd. But Docker is the most popluar runtime out there

A container is a runtime instance of an image what the image becomes in memory when executed (that is, an image with state, or a user process). You can see a list of your running containers with the command, docker ps, just as you would in Linux.

A container runs natively on Linux and shares the kernel of the host machine with other containers. It runs a discrete process, taking no more memory than any other executable, making it lightweight.

Docker allows you to package an application with all of its dependencies into a standardized unit for software development. Stop Complaning that it worked in my machine.

Docker Engine is a client-server application with these major components:
A server which is a type of long-running program called a daemon process (the dockerd command).
A REST API which specifies interfaces that programs can use to talk to the daemon and instruct it what to do.
A command line interface (CLI) client (the docker command).

Difference between Docker Create, Start and Run
Start will start any stopped containers. This includes freshly created containers.
Run is a combination of create and start. It creates the container and starts it.
Create adds a writeable container on top of your image and sets it up for running whatever command you specified in your CMD. The container ID is reported back but it’s not started.
```

#### Containerization is increasingly popular because containers are:
```
1. Flexible: Even the most complex applications can be containerized.
2. Lightweight: Containers leverage and share the host kernel.
3. Interchangeable: You can deploy updates and upgrades on-the-fly.
4. Portable: You can build locally, deploy to the cloud, and run anywhere.
5. Scalable: You can increase and automatically distribute container replicas.
6. Stackable: You can stack services vertically and on-the-fly.
```
#### Docker Image
```
image is an executable package that includes everything needed to run an application--the code, a runtime, libraries, environment variables, and configuration files.

Alpine Based Docker Images are very light weight, base image of Alpine linux is ~4mb. Ship only dependencies which are required for your application. 
Alpine is about 30x smaller than Debian.

Does Your Docker Image OS Need to Match Your Host OS?
Nope.

Docker Image
A Docker image gets built by running a Docker command

If you do not want to use the cache at all, you can use the --no-cache=true option on the docker build
Docker looks for an existing image in its cache that it can reuse, rather than creating a new (duplicate) image.

Adding Metadata to Your Docker Images With Labels
The format is LABEL <key>=<value> and you can add more than 1.
LABEL version="1.0" maintainer="Nick Janetakis <nick.janetakis@gmail.com>"

This expects you to have a Dockerfile in the current directory.
docker build . --label "version=1.0" --label "maintaner=Nick Janetakis <nick.janetakis@gmail.com>"

The WORKDIR instruction allows you to set a specific path in one spot, and then most instructions (RUN and COPY to name a few) will execute in the context of that WORKDIR.
```

#### Namespaces and cgroups
```
Containers are uses Linux namespaces and cgroups. Namespaces let you virtualize system resources, like the file system or networking. Cgroups provide a way to limit the amount of resources like CPU and memory that each container can use. 

Docker uses a technology called namespaces to provide the isolated workspace called the container. 
Docker creates a set of namespaces for that container
These namespaces provide a layer of isolation, Each aspect of a container runs in a separate namespace 

The pid namespace: Process isolation (PID: Process ID).
The net namespace: Managing network interfaces (NET: Networking).
The ipc namespace: Managing access to IPC resources (IPC: InterProcess Communication).
The mnt namespace: Managing filesystem mount points (MNT: Mount).
The uts namespace: Isolating kernel and version identifiers. (UTS: Unix Timesharing System).

Control Groups 
Docker Engine on Linux also relies on another technology called control groups (cgroups). A cgroup limits an application to a specific set of resources. 

Union File System
Union file systems, or UnionFS, are file systems that operate by creating layers, making them very lightweight and fast. e.g  AUFS, btrfs, vfs, and DeviceMapper.

Container Format 
Docker Engine combines the namespaces, control groups, and UnionFS into a wrapper called a container format. The default container format is libcontainer
```

#### Docker v/s Virtual Machines
```
Docker Container are Isolated process, they are not Virtual Machine. 
Docker uses host OS kernel, there is no custom or additional kernel inside container.
It is slightly different than a regular process because the Docker daemon along with the Linux kernel do a few things to ensure it runs in total isolation.
```

#### Docker Commands 
```
The Difference between COPY and ADD in a Dockerfile
COPY takes in a src and destination. It only lets you copy in a local file or directory from your host into the Docker image itself.
ADD lets you do that too, but it also supports 2 other sources. First, you can use a URL instead of a local file / directory. Secondly, you can extract a tar file from the source directly into the destination.

Chain Your Docker RUN Instructions to Shrink Your Images
wget archive.tar && tar -xvf archive.tar && archive.tar

The Difference between RUN and CMD in a Dockerfile
RUN lets you execute commands inside of your Docker image.
CMD lets you define a default command to run when your container starts
It happens when you run an image.
```

#### Things around Docker
```
Dockerfile
Dockerfile is a file that you create which in turn produces a Docker image when you build it.

Docker Compose
It lets you quickly spin up and destroy 1 or many containers. It is what you’ll use in your day to day in development, CI and in some cases even production.

Kitematic
An optional tool that lets you visually manage Docker. It is an alternative to using the Docker CLI.

Docker Hub / Store
The Docker Hub and Docker Store are websites you can visit to find Docker images and even store your own (both publicly and privately). 
```
#### Docker Networking
```
Docker acts as a firewall for your Dockerized services? 
Docker run command: -p 8000:8000. The format is HOST:CONTAINER, and that will bind the container’s port to the host on the ports you specify, which in turn makes it accessible to the outside world. If you supplied -p 8000 it would get bound to the host on a random port.

Specify DNS servers for Docker
sudo nano /etc/docker/daemon.json
{
	"dns": ["8.8.8.8", "8.8.4.4"]
}
Detailed Debugging
{
  "debug": true
}

You can also tunnel the Docker socket over SSH to remotely run commands
ssh -i <path-to-ssh-key> -NL localhost:2374:/var/run/docker.sock username@<ssh-host> &
 docker -H localhost:2374 info

These are things you typically wouldn’t want accessible from the internet, but you would want them accessible to your web application containers running on the same Docker network.
If that’s the case you’ll need to use EXPOSE or --expose
Containers on the same network can talk to each other over their exposed ports without having to publish ports back to the Docker host with -p.
```
#### Docker Tips
```
Docker Without Sudo
Add a docker group and then add your user to it:
sudo groupadd docker
sudo usermod -aG docker $USER
Docker daemon binds to a Unix socket which is owned by the root user. With the above change, it makes the socket accessible by $USER

Redirect a Container's File Onto Your Docker Host
docker run --rm alpine cat /etc/hosts > /tmp/alpinehosts

 Exec form and shell form commands
Runs your CMD’s binary as is, along with any arguments you optionally pass in.

Example CMD:
CMD ["gunicorn", "-c", "python:config.gunicorn", "hello.app:create_app()"]

Shell Form
Runs your CMD’s binary through a shell which has the added benefit of using any shell functionality you want (such as using pipes and &&, etc.).
Example CMD:
CMD gunicorn -c "python:config.gunicorn" "hello.app:create_app()"


Shell form sounds better in theory, but it can mess with signal processing. It also means the shell process ends up being PID 1 instead of whatever binary you’re running in your CMD.

docker container rm $(docker container ls -a -q)         # Remove all containers
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker run -d -p 4000:80 friendlyhello         # Same thing, but in detached mode
docker run -p 4000:80 friendlyhello  # Run "friendlyname" mapping port 4000 to 80
```

#### Docker Container Monitoring
```
Docker has a built in command to let you see how much CPU, memory, network I/O and block I/O your containers are using.

PP-C02WK22GG8WN:cheatsheet ankit.timbadia$ docker stats 57762b30d411
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
57762b30d411        hello-world-nginx   0.00%               1.652MiB / 1.952GiB   

cAdvisor (Container Advisor) provides container users an understanding of the resource usage and performance You can run a single cAdvisor to monitor the whole machine. 
Has a good Web Interface / Rest API 
```
#### Things around Volume Management in Docker
```
Bind Mounts is usefule for Development, mounting the local directory to Docker Container
For Production use case use Volumes

Docker CE on Ubuntu supports overlay2 and aufs storage drivers.
Docker CE uses the overlay2 storage driver by default

Remove Dangling Docker Images

* Docker system prune which will not only remove dangling images but it will also remove all stopped containers, all networks not used by at least 1 container, all dangling images and build caches.
* Show Total Disk Space Used by Docker
* docker system df
PP-C02WK22GG8WN:cheatsheet ankit.timbadia$ docker system df
TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
Images              11                  1                   3.829GB             3.821GB (99%)
Containers          1                   1                   2B                  0B (0%)
Local Volumes       4                   1                   524.8kB             524.3kB (99%)
Build Cache         0                   0                   0B                  0B
You can pry even deeper by using the -v flag (verbose).

Testing out a read-only container:
docker container run --rm --read-only alpine:3.7 touch hello.txt
touch: hello.txt: Read-only file system
For example if you wanted to make /run writeable you could do --tmpfs /run
Using --tmpfs is nice because it doesn’t write a volume back to your Docker host.

Avoid Running Out of Disk Space from Container Logs
daemon.json 
"log-driver": "json-file",
"log-opts": {
  "max-size": "10m",
  "max-file": "1000"
}
```