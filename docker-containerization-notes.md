# Docker --- Linux & Cloud Lab

> **Purpose:** Revision notes for understanding Docker fundamentals and
> using Docker from the terminal.\
> **Chapter:** Docker & Containerization\
> **Environment:** Linux / Arch Linux (commands also work on other
> systems with Docker installed)

------------------------------------------------------------------------

## 1. What is Docker?

**Docker** is a platform for building, packaging, distributing, and
running applications in **containers**.

An application usually depends on more than its source code:

-   A runtime (Python, Node.js, Java, etc.)
-   Libraries and packages
-   System tools and shared libraries
-   Configuration and environment variables
-   A particular version of dependencies

Installing and configuring all of these separately on every machine can
cause the classic *"it works on my machine"* problem.

Docker helps package the application and its required runtime
environment into an **image**. That image can then be used to create
containers on other machines that have a compatible container runtime.

### Container-box analogy

Think of a container as a standardized box carrying an application and
its runtime requirements. The box makes the application easier to move
and run consistently.

The analogy has limits: a Docker container is not a complete physical
computer or a full virtual machine. It shares the host machine's kernel
and is isolated using operating-system features.

------------------------------------------------------------------------

## 2. Image vs Container

These two terms are fundamental.

### Docker image

An **image** is a read-only template used to create containers.

It contains the filesystem layers and metadata needed to run an
application, such as:

-   A base filesystem / base image
-   Application files
-   Installed dependencies
-   Default command or entry point
-   Default environment configuration and metadata

An image is not normally one simple file that you open and run. Docker
stores images as content-addressed layers and metadata. Images can be
downloaded from a registry or built from a Dockerfile.

Examples of image names:

``` bash
ubuntu:24.04
python:3.12
nginx:latest
```

The part after `:` is the **tag**. It commonly identifies a version or
variant. If no tag is supplied, Docker generally assumes `latest`.

### Docker container

A **container** is a runnable instance created from an image.

When Docker creates a container, it adds a writable layer on top of the
image's read-only layers. The container also gets its own process view,
networking configuration, and other isolated resources.

A container can be:

-   Created
-   Started
-   Stopped
-   Restarted
-   Removed

A stopped container still exists until it is removed.

### Relationship

``` text
Dockerfile
   |
   | docker build
   v
Docker image
   |
   | docker run
   v
Docker container (running instance)
```

One image can be used to create many containers. They start from the
same image but have separate writable container layers and runtime
state.

**Remember:** - Image = template / packaged application environment. -
Container = an instance created from that image. - Dockerfile =
instructions used to build an image. - Registry = a service that stores
and distributes images.

------------------------------------------------------------------------

## 3. How Docker works

Docker uses a client-server architecture.

-   **Docker CLI:** the `docker` command you type in the terminal.
-   **Docker daemon (`dockerd`):** the background service that manages
    images, containers, networks, and volumes.
-   **Docker Engine:** the core Docker platform, including the daemon
    and APIs.
-   **Container runtime:** the lower-level components used to start and
    manage container processes.
-   **Registry:** a remote image store, such as Docker Hub.

A simplified command flow:

``` text
You type: docker run nginx
          |
          v
      Docker CLI
          |
          v
    Docker daemon
       /      \
      /        \
 Find image   Create and start container
 locally      from the image
      |
 If missing, pull it from a registry
```

On Linux, containers use kernel features such as **namespaces** and
**control groups (cgroups)**:

-   Namespaces provide isolation for things such as process IDs,
    networking, mounts, and hostnames.
-   cgroups help account for and limit resource usage such as CPU and
    memory.

Docker does not normally boot a separate guest operating-system kernel
for every container. Containers share the host kernel.

------------------------------------------------------------------------

## 4. What is a Dockerfile?

A **Dockerfile** is a text file containing instructions for building an
image.

Example: a small Python application.

``` dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

### Common Dockerfile instructions

  -----------------------------------------------------------------------
  Instruction                         Purpose
  ----------------------------------- -----------------------------------
  `FROM`                              Selects the base image. Usually the
                                      first instruction.

  `WORKDIR`                           Sets the working directory for
                                      later instructions.

  `COPY`                              Copies files from the build context
                                      into the image.

  `ADD`                               Copies files and has additional
                                      archive/URL behavior; use `COPY`
                                      for ordinary copies.

  `RUN`                               Executes a command while building
                                      the image.

  `ENV`                               Sets environment variables in the
                                      image.

  `EXPOSE`                            Documents the port the application
                                      is expected to listen on; does not
                                      publish it to the host.

  `CMD`                               Supplies the default
                                      command/arguments when a container
                                      starts.

  `ENTRYPOINT`                        Defines the main executable for the
                                      container.

  `ARG`                               Defines a build-time variable.

  `USER`                              Selects the user used for
                                      subsequent build steps and runtime.

  `VOLUME`                            Declares a mount point intended for
                                      persistent/external data.
  -----------------------------------------------------------------------

### `RUN` vs `CMD`

-   `RUN` executes during **image build**. Its result becomes part of an
    image layer.
-   `CMD` defines the default command that runs when a container starts.

### `CMD` vs `ENTRYPOINT`

-   `CMD` is commonly used to provide a default command or default
    arguments.
-   `ENTRYPOINT` is commonly used to define the main executable.
-   They can be combined. Runtime arguments can replace or extend
    defaults depending on how they are written.

### Build an image

Run this in the directory containing the Dockerfile:

``` bash
docker build -t my-app:1.0 .
```

-   `-t` gives the image a name and optional tag.
-   `.` is the **build context**: the files Docker is allowed to access
    for the build.

Build without using the build cache:

``` bash
docker build --no-cache -t my-app:1.0 .
```

A `.dockerignore` file can exclude unnecessary files from the build
context, for example `.git`, local virtual environments, caches, and
secret files.

------------------------------------------------------------------------

## 5. Image layers and build cache

Docker images are generally made of multiple layers.

For example, a Dockerfile might: 1. Start from a Python base image. 2.
Install dependencies. 3. Copy application source code.

Docker can reuse unchanged layers when rebuilding an image. This is the
**build cache**.

A practical ordering pattern is to copy dependency files and install
dependencies before copying frequently changing source files:

``` dockerfile
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
```

If only application source files change, Docker may reuse the
dependency-installation layer.

**Important:** An image is not simply a live snapshot of a running
container. It is a built, layered template. A container's writable
changes do not automatically modify the original image.

------------------------------------------------------------------------

## 6. Running containers

### Basic run

``` bash
docker run nginx
```

Docker creates and starts a new container from the `nginx` image. If the
image is not present locally, Docker attempts to pull it from the
configured registry.

### Run in the background

``` bash
docker run -d nginx
```

`-d` means detached mode.

### Give a container a name

``` bash
docker run --name web-server -d nginx
```

A custom name is easier to use than remembering a generated container
ID.

### Interactive terminal

``` bash
docker run -it ubuntu:24.04 bash
```

-   `-i` keeps standard input open.
-   `-t` allocates a terminal.
-   `bash` is the command started inside the container.

Some minimal images do not include Bash. In that case, try `sh`.

### Automatically remove a container when it exits

``` bash
docker run --rm hello-world
```

Useful for temporary containers. Do not use `--rm` if you need to keep
the stopped container for later inspection.

------------------------------------------------------------------------

## 7. Ports and port mapping

A process inside a container can listen on a container port. To reach it
through a port on the host, publish a port:

``` bash
docker run -d --name web -p 8080:80 nginx
```

Format:

``` text
-p <host_port>:<container_port>
```

In this example:

-   Host port: `8080`
-   Container port: `80`
-   Open `http://localhost:8080` on the Docker host.

**Important distinctions:**

-   The application inside the container must actually listen on the
    container port.
-   `EXPOSE` in a Dockerfile is documentation/metadata; it does not
    publish a port.
-   `-p` publishes a port from the container to the host.
-   Containers on the same suitable Docker network can often communicate
    directly using container/service names without publishing every port
    to the host.

------------------------------------------------------------------------

## 8. Environment variables

Environment variables provide runtime configuration without hard-coding
every setting into the image.

``` bash
docker run -d \
  --name app \
  -e APP_MODE=development \
  -e PORT=5000 \
  my-app:1.0
```

Read an environment variable inside a container's process environment:

``` bash
docker exec app printenv APP_MODE
```

A Dockerfile can also define defaults:

``` dockerfile
ENV PORT=5000
```

A value passed with `docker run -e` can override the image's default.

**Security note:** Do not bake passwords, API keys, or other secrets
into a Dockerfile or public image. Use an appropriate secrets-management
approach for the environment in which the application runs.

------------------------------------------------------------------------

## 9. Container lifecycle and inspection

### List containers

``` bash
docker ps       # running containers
docker ps -a    # all containers, including stopped ones
```

### Start, stop, restart

``` bash
docker start <container_name_or_id>
docker stop <container_name_or_id>
docker restart <container_name_or_id>
```

`docker stop` asks the main process to stop gracefully and may force
termination after a timeout.

### Inspect a container

``` bash
docker inspect <container_name_or_id>
```

Displays detailed JSON information, including configuration, network
settings, mounts, and state.

### View logs

``` bash
docker logs <container_name_or_id>
docker logs -f <container_name_or_id>
```

`-f` follows the log output.

### Execute a command inside a running container

``` bash
docker exec -it <container_name_or_id> bash
```

If Bash is unavailable:

``` bash
docker exec -it <container_name_or_id> sh
```

This starts an additional process inside the existing running container.
It does not create a new container.

### Remove a container

``` bash
docker rm <container_name_or_id>
```

A running container normally needs to be stopped first:

``` bash
docker stop web
docker rm web
```

For a disposable container, `docker rm -f <name>` force-removes it. Use
this carefully.

------------------------------------------------------------------------

## 10. Volumes and persistent data

A container's writable layer is tied to that container. If the container
is removed, data stored only in that writable layer is removed too.

For data that must survive container replacement, use **volumes** or
**bind mounts**.

### A. Named volume

A named volume is managed by Docker and has a name you choose.

Create one:

``` bash
docker volume create app-data
```

Use it:

``` bash
docker run -d \
  --name database \
  --mount type=volume,src=app-data,dst=/var/lib/app \
  my-app:1.0
```

Alternative `-v` syntax:

``` bash
docker run -d -v app-data:/var/lib/app my-app:1.0
```

The data in the volume can remain after the container is removed. The
volume itself must be removed separately.

### B. Anonymous volume

An anonymous volume is created without a user-chosen name:

``` bash
docker run -d -v /var/lib/app my-app:1.0
```

Docker assigns it an identifier. It is still a volume, not merely the
container's writable layer.

### C. Bind mount

A bind mount connects a specific path on the host to a path inside the
container.

``` bash
docker run -d \
  --name dev-app \
  --mount type=bind,src="$PWD",dst=/app \
  my-app:1.0
```

Alternative syntax:

``` bash
docker run -d -v "$PWD":/app my-app:1.0
```

Use cases: - Editing source code on the host while the container uses
the same files. - Sharing a host directory with a container. -
Development workflows.

The host path must exist or be handled appropriately by the Docker
command and mount syntax. Bind mounts expose the selected host path to
the container, so mount only directories the container needs.

### Named volume vs bind mount

  -----------------------------------------------------------------------
  Feature                 Named volume            Bind mount
  ----------------------- ----------------------- -----------------------
  Source                  Docker-managed storage  Specific host
                                                  filesystem path

  Host path management    Docker manages it       User chooses the path

  Common use              Database/application    Source code and host
                          persistent data         files

  Portability             Usually easier to move  Depends on host
                          between setups          directory layout
  -----------------------------------------------------------------------

### Volume commands

``` bash
docker volume ls
docker volume create <volume_name>
docker volume inspect <volume_name>
docker volume rm <volume_name>
docker volume prune
```

`docker volume prune` removes unused volumes. Review what will be
deleted before confirming. Do not assume a volume is disposable just
because no current container is using it.

------------------------------------------------------------------------

## 11. Docker networks

Containers often need to communicate---for example, a web application
needs to reach a database.

Docker networks provide connectivity between containers.

### List networks

``` bash
docker network ls
```

### Create a network

``` bash
docker network create app-network
```

### Run containers on the same network

``` bash
docker run -d --name database --network app-network my-database
docker run -d --name backend --network app-network my-backend
```

On a user-created bridge network, containers can generally reach one
another using container names as DNS names. For example, the backend can
use `database` as the database hostname, assuming the database service
is configured to accept connections.

### Connect or disconnect a container

``` bash
docker network connect app-network <container_name>
docker network disconnect app-network <container_name>
```

### Inspect and remove networks

``` bash
docker network inspect app-network
docker network rm app-network
docker network prune
```

`docker network prune` removes unused networks. Check before confirming.

### Common network types

-   **Bridge:** common for containers on one Docker host. User-created
    bridge networks support convenient name-based communication.
-   **Host:** the container shares the host's network namespace on
    supported Linux setups; isolation is reduced.
-   **None:** disables normal external networking for the container.
-   **Overlay:** connects containers across multiple Docker hosts in
    supported swarm setups.

------------------------------------------------------------------------

## 12. Docker Hub and image registries

A **registry** stores and distributes container images. Docker Hub is a
widely used public registry.

### Search for an image

``` bash
docker search nginx
```

### Download (pull) an image

``` bash
docker pull nginx:latest
```

### List local images

``` bash
docker images
```

### Log in and out

``` bash
docker login
docker logout
```

### Tag an image for your account

``` bash
docker tag my-app:1.0 <dockerhub_username>/my-app:1.0
```

### Push an image

``` bash
docker push <dockerhub_username>/my-app:1.0
```

You need to be authenticated and have permission to push to the target
repository.

**Before publishing:** ensure the image does not contain passwords, API
keys, private files, local databases, or other confidential data.

------------------------------------------------------------------------

## 13. Image management and cleanup

``` bash
docker images
docker image ls
docker rmi <image_name_or_id>
docker image prune
```

-   `docker rmi` removes an image, provided it is not being used by a
    container in a way that prevents removal.
-   `docker image prune` removes dangling/unused image data according to
    Docker's prune behavior.
-   `docker image prune -a` is broader and can remove all images not
    used by existing containers. Use with care.

Useful disk-usage overview:

``` bash
docker system df
```

General cleanup command:

``` bash
docker system prune
```

This removes unused Docker objects such as stopped containers, unused
networks, and dangling images. Options can broaden the cleanup. Read the
command's prompt and documentation before confirming, especially when
you have data you want to keep.

------------------------------------------------------------------------

## 14. A practical workflow

A typical simple application workflow looks like this:

1.  Write the application.
2.  Create a `Dockerfile`.
3.  Add a `.dockerignore`.
4.  Build the image.
5.  Run a container from the image.
6.  Publish the required port.
7.  Supply runtime configuration with environment variables.
8.  Use volumes for data that must persist.
9.  Use a Docker network when multiple containers need to communicate.
10. Inspect logs and container state while debugging.
11. Tag and push the image to a registry if you need to share it.

Example:

``` bash
# Build
docker build -t demo-web:1.0 .

# Run in background, name it, and publish a port
docker run -d --name demo-web -p 8080:5000 demo-web:1.0

# Check status and logs
docker ps
docker logs demo-web

# Open a shell if the image contains one
docker exec -it demo-web sh

# Stop and remove the container when finished
docker stop demo-web
docker rm demo-web
```

Adjust the container port to the port your application actually listens
on.

------------------------------------------------------------------------

## 15. What containers are suitable for---and their limitations

### Good use cases

Containers are useful for:

-   Packaging web applications and APIs with their runtimes and
    dependencies.
-   Running databases and supporting services for development or
    deployment.
-   Reproducing development/test environments.
-   Running background workers and scheduled jobs.
-   Separating services in a multi-service application.
-   CI/CD build and test environments.
-   Deploying the same application image across compatible environments.

### Limitations and things to understand

1.  **Shared host kernel:** ordinary Linux containers share the host
    kernel. They are not full virtual machines with their own
    independent kernel.
2.  **Kernel compatibility:** an image packages user-space files and
    dependencies, not a replacement host kernel. Kernel-dependent
    features must be supported by the host.
3.  **Not every application is automatically portable:** hardware
    access, kernel modules, special devices, architecture, OS
    assumptions, and external services can require extra configuration.
4.  **Persistent data needs planning:** data stored only in a
    container's writable layer is not a reliable persistence strategy.
5.  **Networking is not automatic magic:** ports, networks, DNS,
    firewalls, and application listen addresses must be configured
    correctly.
6.  **Containers still consume resources:** they use host CPU, memory,
    disk, and network resources. Resource limits may be needed.
7.  **Security still matters:** containers provide isolation, but a
    badly configured container can expose host resources. Avoid
    privileged mode unless there is a clear need; avoid running as root
    when practical; use trusted images and keep them updated.
8.  **Large images are inefficient:** unnecessary packages and build
    files increase image size and may increase build and distribution
    time.
9.  **Stateful systems need care:** databases can run in containers, but
    backups, persistent storage, upgrades, and recovery still need to be
    designed.
10. **GUI or hardware-heavy software may need extra setup:** graphics,
    USB devices, GPUs, audio, and other host resources may require
    explicit access and drivers.

### Docker vs virtual machines (high level)

  -----------------------------------------------------------------------
  Docker container                    Virtual machine
  ----------------------------------- -----------------------------------
  Shares the host kernel in the       Runs a guest operating system with
  normal Linux-container model        its own kernel

  Usually starts quickly              Usually has more startup overhead

  Packages application user space and Includes a full guest OS
  dependencies                        environment

  Often lighter for application       Useful when a separate OS/kernel
  deployment                          boundary is required
  -----------------------------------------------------------------------

Neither replaces the other in every situation. The right choice depends
on isolation, kernel/OS requirements, hardware access, and operational
needs.

------------------------------------------------------------------------

## 16. Command reference

### Images

``` bash
docker images
docker pull <image>
docker build -t <image>:<tag> .
docker build --no-cache -t <image>:<tag> .
docker rmi <image>
docker image prune
```

### Containers

``` bash
docker run <image>
docker run -d <image>
docker run --name <name> <image>
docker run -it <image> sh
docker ps
docker ps -a
docker start <container>
docker stop <container>
docker restart <container>
docker inspect <container>
docker rm <container>
```

### Ports and environment

``` bash
docker run -p <host_port>:<container_port> <image>
docker run -e KEY=value <image>
```

### Troubleshooting

``` bash
docker logs <container>
docker logs -f <container>
docker exec -it <container> bash
docker exec -it <container> sh
```

### Docker Hub / registry

``` bash
docker search <term>
docker pull <image>
docker login
docker logout
docker tag <local_image> <username>/<repository>:<tag>
docker push <username>/<repository>:<tag>
```

### Volumes

``` bash
docker volume ls
docker volume create <volume>
docker volume inspect <volume>
docker volume rm <volume>
docker volume prune
docker run -v <volume>:<container_path> <image>
docker run -v <host_path>:<container_path> <image>
```

### Networks

``` bash
docker network ls
docker network create <network>
docker network inspect <network>
docker network connect <network> <container>
docker network disconnect <network> <container>
docker network rm <network>
docker network prune
```

------------------------------------------------------------------------

## 17. Quick revision: remember these distinctions

  -----------------------------------------------------------------------
  Term                                Meaning
  ----------------------------------- -----------------------------------
  Dockerfile                          Build instructions

  Image                               Read-only, layered template

  Container                           Runnable instance of an image

  Registry                            Stores and distributes images

  Docker Hub                          A public image registry

  `docker build`                      Builds an image from a build
                                      context and Dockerfile

  `docker run`                        Creates and starts a new container

  `docker start`                      Starts an existing stopped
                                      container

  `docker exec`                       Runs another process inside an
                                      existing running container

  Port mapping                        Makes a container port reachable
                                      through a host port

  Named volume                        Docker-managed persistent storage

  Bind mount                          Maps a chosen host path into a
                                      container

  Network                             Allows containers and other
                                      endpoints to communicate
  -----------------------------------------------------------------------

**One-line mental model:**

> Build an image from a Dockerfile → run containers from that image →
> connect them with networks → persist important data with volumes →
> inspect logs and state when debugging.

------------------------------------------------------------------------

## 18. Suggested hands-on revision checklist

-   [ ] Pull an image from Docker Hub.
-   [ ] List local images.
-   [ ] Run a container in the foreground and in detached mode.
-   [ ] Give a container a custom name.
-   [ ] List running and stopped containers.
-   [ ] Publish a port and access the application from the host.
-   [ ] Pass an environment variable to a container.
-   [ ] Read logs and open a shell in a running container.
-   [ ] Build a small image using a Dockerfile.
-   [ ] Rebuild it and observe how the build cache behaves.
-   [ ] Create a named volume and verify that its data survives
    container removal.
-   [ ] Bind-mount a project directory for development.
-   [ ] Create a user-defined network and connect two containers.
-   [ ] Inspect an image, container, volume, and network.
-   [ ] Practice cleanup commands carefully.

------------------------------------------------------------------------
