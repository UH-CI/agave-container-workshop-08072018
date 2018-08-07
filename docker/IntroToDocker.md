# Intro to Docker

Intro slides: https://docs.google.com/presentation/d/1gQfxs7t1McMLZPeZADWx5Rv9h-RUj6CaKQy99WDACGQ/edit?usp=sharing

### Initial setup
Typically, accessing the docker daemon requires root or to be in the docker group. For the purposes of this introduction,
 we can simply do everything as the root user:
```
$ sudo su - root
```

Make sure you can access the docker daemon; you can verify this by checking the version:
```
$ docker version
Client:
 Version:	17.12.1-ce
 API version:	1.35
 Go version:	go1.10.1
 Git commit:	7390fc6
 Built:	Wed Apr 18 01:23:11 2018
 OS/Arch:	linux/amd64

Server:
 Engine:
  Version:	17.12.1-ce
  API version:	1.35 (minimum version 1.12)
  Go version:	go1.10.1
  Git commit:	7390fc6
  Built:	Wed Feb 28 17:46:05 2018
  OS/Arch:	linux/amd64
  Experimental:	false

```

Create a test directory to contain your docker work:
```
$ mkdir docker; cd docker
```

### Docker Images and Tags, Docker Hub, and Pulling Images
A Docker image is a container template from which one or more containers can be run. It is a rooted filesystem that,
by definition, contains all of the file dependencies needed for whatever application(s) will be run within the
containers launched from it. The image also contains metadata describing options available to the operator running
containers from the image.

One of the great things about Docker is that a lot of software has already been packaged into Docker images. One source
of 100s of thousands of public images is the official docker hub: https://hub.docker.com.

The docker hub contains images contributed by individual users and organizations as well as "official images". Explore
the official docker images here: https://hub.docker.com/explore/

For example, there is an official image for the Python programming language: https://hub.docker.com/_/python/

Docker supports the notion of image tags, similar to tags in a git repository. Tags identify a specific version of an
image.

The full name of an image on the Docker Hub is comprised of components separated by slashes. The components include a
"repository" (which could be owned by an individual or organization), the "name", and the "tag". For example, an image
with the full name
```
jstubbs/workshop2018_test:0.1
```
would be in the "jstubbs" repository and have a tag of "0.1". TACC maintains multiple repositories on the Docker Hub
including:
```
tacc
taccsciapps
agaveapi
abaco
```
Official images such as the python official image are not owned by a repository, but all other images are.

To pull an image off Docker Hub use the `docker pull` command

```
$ docker pull python
Using default tag: latest
latest: Pulling from library/python
cc1a78bfd46b: Pull complete
. . .
```

As indicated in the output, if no tag is specified the "latest" tag is pulled. You can verify that the image is
available on your local machine using the `docker images` command:
```
$ docker images
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
jstubbs/workshop2018_test   0.1                 f4eccddfaba7        46 seconds ago      1.16MB
<none>                      <none>              f9b10c12ce9f        15 minutes ago      156MB
busybox                     latest              e1ddd7948a1c        6 days ago          1.16MB
ubuntu                      16.04               7aa3602ab41e        11 days ago         115MB
```


### Building Images From a DockerFile
We can build images from a text file called a Dockerfile. You can think of a Dockerfile as a recipe for creating images.
The instructions within a dockerfile either add files/folders to the image, add metadata to the image, or both.

#### The FROM instruction
We can use the `FROM` instruction to start our new image from a known image. This should be the first line of our Dockerfile. We will start our image from an official Ubuntu 16.04 image:

```
FROM ubuntu:16.04

```

#### The RUN instruction
We can add files to our image by running commands with the `RUN` instruction. We will use that to install `wget` via `apt`. Keep in mind that the the docker build cannot handle interactive prompts, so we use the `-y` flag in `apt`. We also need to be sure to update our apt packages.

The Dockerfile will look like this now:
```
FROM ubuntu:16.04

RUN apt-get update && apt-get install -y wget
```

#### The ADD instruction
We can also add local files to our image using the `ADD` instruction. We can add a file `test.txt` in our local directory to the `/root` directory in our container with the following instruction:

```
ADD test.txt /root/text.txt
```

#### Building the Image
To build an image from a Dockerfile we use the `docker build` command. We use the `-t` flag to tag the image: that is, give our image a name. We also need to specify the working directory for the buid. We specify the current working directory using a dot (.) character:
```
docker build -t workshop_test .
```

A complete Dockerfile for building a "real" image containing the qiime bioinformatics pipeling can be found in the workshop repository:

https://github.com/UH-CI/agave-container-workshop-20180806/blob/master/docker/qiime/Dockerfile


### Running a Docker Container
We use the `docker run` command to run containers from an image. We pass a command to run in the container.

#### Running and Attaching to a Container
To run a container and attach to it in one command, use the `-it` flags. Here we run `bash` in a container from the ubuntu image:
```
docker run -it ubuntu bash
```

#### Running a Container in Daemon mode ####
We can also run a container in the background. We do so using the `-d` flag:
```
docker run -d ubuntu sleep infinity
```
Keep in mind that the command given to the `docker run` statement will be given PID 1 in the container, and as soon as this process exits the container will stop.

#### Running Additional Commands in a Running Container ####
Finally, we can execute commands in a running container using the `docker exec` command. First, we need to know the container id, which we can get through the `docker ps` command:

```
~ $ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
a2f968b8443f        ubuntu:16.04        "sleep infinity"    9 seconds ago       Up 8 seconds                            awesome_goldwasser
```

Here we see the container id is `a2f968b8443f`. To execute `bash` in this container we do:
```
docker exec -it a2f968b8443f bash
```
At this point we are attached to the running container. If our bash session exits, the container will keep running because the `sleep infinity` command is still running.

*Note: The `docker ps` command only shows you running containers - it does not show you containers that have exited. In order to see all containers
on the system use `docker ps -a`.


### Removing Docker Containers ###
We can remove a docker container using the `docker rm` command (optionally passing `-f` to "force" the removal if the container is running). You will need the container name or id to remove the container:
```
$ docker rm -f a2f968b8443f
```


### Mounting Volumes ###
By default, when you run a docker container, its file system is completely isolated from the host file system and only
contains the files that were added to the image. Through the use of "volume mounts", we can add files or directories
from the host to the container at run time to accomplish various goals.

Reasons to do use volume mounts include:
1. Save/persist files produced by the container beyond the container's life.
2. Parameterize the container with additional configuration files at run time.
3. Add security sensitive files such as SSL certificates to a public image.

To mount a volume into a container use the `-v` flag: the format is `-f <host_path>:<conatiner_path>`. For example:

```
docker run -it -v /root/test:/data ubuntu bash
```

Will create and run a docker conainer from the ubuntu image with the `/root/test` directory on the host mounted at `/data`
from within the container. Note that changes made within the container persist onto the host!

```
Exercise. Create a container with a volume mount to some directory on the host and add create a text file with some
basic text from within the container. Verify that the text file exists on the host.
```


### Container Networking and Exposing Ports ###
In addition to an isolated file system, containers enjoy an isolated network stack on a special Docker network. In
general, the kind of special network used for containers depends on how Docker is set up. When multiple docker computers
are involved (called a "Docker swarm cluster") networks can be built to connect containers across hosts. For this
basic introduction however, we will restrict our discussion to the case of a single computer running Docker.

In the case of a single Docker host, containers are added to a "bridge" network. All the details of Docker bridge networking
are beyond the scope of this course, but here are some key facts:

* All containers get assigned an IP on the bridge network.
* By default, this IP address is in the 172.17.0.0 subnet and ports on it are available on the host only.
* A port on a container's IP address can be "mapped" to a (potentially different) port on the host's IP address to make it available externally.

We can determine which IP address a container was assigned by using the `docker inspect <container_id>` command. For example:
```
$ docker inspect 7443bd1063b9 | grep IPAddress
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
```
indicates that container with ID `7443bd1063b9` has IP address `172.17.0.2`.

#### Mapping Ports ####
Container ports can be mapped to the host using the `-p` flag to the `docker run` command: the format is `-p <host_port>:<container_port>`.

For example, the following statement would map port 9000 on the host to port 6379 within a redis container:
```
$ docker run -p 9000:6379 redis
. . .
The server is now ready to accept connections on port 6379
```
Note that we now have two ways to interact with redis: assuming the Redis container had IP address `172.17.0.2` and that the VM had a public IP address of `129.114.17.201`, from the VM, we can make requests to 172.17.0.2:6379 or (from outside) 129.114.17.201:9000.

```
Exercise. Nginx is a popular http proxy and has an official image available on the Docker Hub. By default, nginx listens for requests on port 80. Create an nginx container and map its port 80 to an external port on your VM's IP.
Exercise. Use curl to check that you can a) interact with nginx on port 80 using it's container IP and b) interact with nginx on the mapped port using your VM's IP.

### Building the Jupyter Image ###
Let's build a Docker image that contains a (nearly) identical software environment to that running in our VM:

```
Exercise. Copy or download the Dockerfil source code for the Jupyter image from the following URL: https://github.com/TACC/CSC2018Institute/blob/master/docker/Dockerfile
Exercise. Build a new docker image on your VM using this source.
```


### Running the Jupyter Container
We need to map the 8887 port so that Jupyter is available from outside the VM. We might also want to mount the current working directory on the host to a data directory to save our files after the container is destroyed.

```
docker run -d -it -p 8887:8887 -v $(pwd):/data tacc/csc_jupyter
```
