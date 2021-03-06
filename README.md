# Docker Up & Running
This is a git project to store my notes about the book Docker Up & Running from O'reilly media.
## Highlights:
    - Docker client: docker command to interact with the docker server
    - Docker server: docker command in daemon node. Interacts with containers
    - Docker images: filesystem layers to build a container
    - Docker container: wrapper to a unix process

### Chapter 2: Docker at a glance
    - Docker relies on a client server architecture, the client instruct the server what to do with containers and the server interacts, with them,
    - The docker daemon starts with `docker -d`
    - Dockers are not VM, are wrappers for unix processes.
    - Storing things in the container's file system is not recommended. The recommnended approach is to store persistent information in a central storage system such as S3.
    - Docker containers are made of stack filesystem layers, each identified with a hash.

## Chapter 3: Installing Docker
Installing docker:
    - Ubuntu: `sudo apt-get install docker.io`
    - CentOS: `sudo yum -y install docker.io`
    - MacOS X: `brew install docker boot2docker`

Starting docker server:
1. systemd based systems (Fedora): `sudo systemctl enable docker` + `sudo systemctl start docker`
2. upstart based systems: `sudo update-rc.d docker.io defaults` + `service docker.io start`
3. init.d based systems: `sudo chkconfig docker on` + `service docker start`
4. Non lynux systems:
⋅⋅1. boot2docker: `boot2docker init` + `boot2docker up`.
⋅⋅⋅To set the shell environment type `$(boot2docker shellinit)`.
⋅⋅⋅To connect to a shell on boot2docker type `boot2docker ssh`

⋅⋅2. Docker Machine: Install the executable, and then type `docker-machine create --driver virtualbox <machine_name>`.
⋅⋅⋅To set the shell environment type `$(docker-machine env <machine_name>)`.
⋅⋅⋅You can list the machines running in your local with `docker-machine ls`, and  log into it with `docker-machine ssh <machine_name>`.

⋅⋅3. vagrant: Download the project _coreos-vagrant_ into your project folder:
⋅⋅⋅`git clone https://github.com/coreos/coreos-vagrant.git` and then inside the created folder create a file named _config.rb_ with the configuration of the port to expose docker `echo "\$expose_docker_tcp=2375" >> config.rb`.
⋅⋅⋅Create a file called _user-data_ with the specific [configuration](https://coreos.com/os/docs/latest/cloud-config.html) and then start the vagrant machine with `vagrant up`.
⋅⋅⋅Connect to the machine using `vagrant ssh`.
The last bit is to test it up. The command for this depends on your os `docker run --rm -ti <os>:latest /bin/bash`

## Chapter 4: Working with Docker images
Every Docker image consists of one or more filesystem layers, one build on top of the other. Each line in the docker file creates a new image layer that is stored in docker, so sometimes it makes sense to combine multiple commands in a single line.
** Relevant Fields **
    - MAINTAINER: Contact information for the Docker's file author.
    - LABEL: Metadata in the form of key value pairs
    - USER: Docker runs all the commands with root, but you can change that with this label. Containers run on isolation on the hosts kernel, it is recommended to run containers under the context of a non-privileged user.
    - ENV: Instruction to set shell variables.
    - RUN: Instruction to run commands in the shell
    - ADD: Instruction to copy files from the host system to the docker container.
    - WORKDIR: Instruction to set the working directory from where the commands are run in the container.
    - CMD: Instruction that launches the process to run within the container. It is a good practice to run a single process per container.

To build a new image, `docker build -t <name>:<version> <path to dockerfile>`. After this, you can run the image with `docker run <options> <name>:<version>` some usefull options to include:
    - `-d`: for running in the background.
    - `-p <host_port>:<container_port>`: Maps ports in the container with the hosts ports.
    - `-e <variable>=<value>`: to pass environment variables to the docker container.

You can view the containers running with `docker ps`, stop them with `docker stop <container id>`. 
It is possible to set a registry mirror to avoid downloading the layers from the net every time you build a machine, this is done by passing `--registry-mirror=http://<REGISTRY MIRROR HOST>`, this option can be set by default on the docker daemon, but it is OS specific:

    - Boot2Docker: In the file `/var/lib/boot2docker/profile` add `EXTRA_ARGS="--registry-mirror=..."`
    - Ubuntu: file `/etc/default/docker` add `DOCKER_OPTS="--registry-mirror=...`
    - Fedora: file `/etc/sysconfig/docker` add `OPTIONS="--registry-mirror=..."`
    - CoreOS: file `/etc/systemd/system/docker.service` add `$DOCKER_OPTS $DOCKER_OPT_BIP $DOCKER_OPT_MTU $DOCKER_OPT_IPMASK --registry-mirror="..."` at the end of the `ExecStart` line.


## Chapter 5: Working with Docker Containers ##


