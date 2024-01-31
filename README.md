# Docker Notes

## Index
1. [Introduction to Docker](#introduction-to-docker)
2. [Docker Hub](#docker-hub)
3. [Installation](#installation)
4. [Docker Commands and Usage](#docker-commands-and-usage)
   - [Getting Started](#getting-started)
   - [Managing Containers](#managing-containers)
   - [Managing Images](#managing-images)
   - [Executing Commands](#executing-commands)
   - [Interactive Mode and TTY](#interactive-mode-and-tty)
   - [Port Mapping](#port-mapping)
   - [Volume Mapping](#volume-mapping)
   - [Inspecting a Container](#inspecting-a-container)
   - [Viewing Container Logs](#viewing-container-logs)
   - [Environment Variables](#environment-variables)
5. [Creating Your Own Docker Image](#creating-your-own-docker-image)
   - [Conceptualizing the Process](#conceptualizing-the-process)
   - [Example Dockerfile for a Flask Application](#example-dockerfile-for-a-flask-application)
   - [Explanation of the Dockerfile](#explanation-of-the-dockerfile)
   - [Building and Pushing the Image](#building-and-pushing-the-image)
   - [Docker's Layered Architecture](#dockers-layered-architecture)


## Introduction to Docker
Docker is an open-source platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly.

### Docker Hub
Docker Hub is a cloud-based repository in which Docker users and partners create, test, store, and distribute container images.

### Installation
- Docker is available on various operating systems.
- Installation guide: [Docker Installation](https://docs.docker.com/get-docker/)
- <details><summary>On Linux, install Docker using <code>docker.io</code> package.</summary><code>sudo apt-get install docker.io -y</code></details>

## Docker Commands and Usage

### Getting Started
- **Check Docker Version**
  ```
  docker version
  ```

### Managing Containers
- **Run a Container**
  - Attached mode (default):
    ```
    docker run <image>
    ```
  - Detached mode:
    ```
    docker run -d <image>
    ```
  - Attach to running container:
    ```
    docker attach <container_id>
    ```

- **List Containers**
  - Running containers:
    ```
    docker ps
    ```
  - All containers:
    ```
    docker ps -a
    ```

- **Stop a Container**
  ```
  docker stop <container_id>
  ```

- **Remove a Container**
  ```
  docker rm <container_id>
  ```

### Managing Images
- **List Images**
  ```
  docker images
  ```

- **Remove an Image**
  ```
  docker rmi <image_id>
  ```

- **Pull an Image**
  ```
  docker pull <image_name>
  ```

### Executing Commands
- **Execute Command in Running Container**
  ```
  docker exec <container_id> <command>
  ```

### Tags
- Use tags to specify versions or variants of the same image.

### Interactive Mode and TTY
- **Interactive Mode**
  The `-i` flag stands for 'interactive', keeping STDIN open even if not attached. It allows you to interact with the Docker container via the command line. For example, to start a container in interactive mode:
  ```
  docker run -i <image>
  ```

- **Allocate Pseudo-TTY**
  The `-t` flag allocates a pseudo-TTY, which simulates a terminal, like SSH. It makes it possible to input commands into the container and see the output in a more readable format. To start a container with a pseudo-TTY:
  ```
  docker run -t <image>
  ```

The `-i` and `-t` flags are often used together as `-it` to create an interactive shell into the container. This combination allows for a more intuitive and familiar command-line experience when interacting with containers, as it simulates a regular terminal session, with input and formatted output.

```
docker run -it <image>
```

### Port Mapping
- Map container port to host port:
  ```
  docker run -p <host_port>:<container_port> <image>
  ```

### Volume Mapping
- Map volume from host to container:
  ```
  docker run -v <host_directory>:<container_directory> <image>
  ```

### Inspecting a Container
- Detailed container information:
  ```
  docker inspect <container_id>
  ```

### Viewing Container Logs
- View logs:
  ```
  docker logs <container_id>
  ```

### Environment Variables
- Set environment variables when running a container:
  ```
  docker run -e <VAR_NAME>=<value> <image>
  ```
- View environment variables in running container:
  ```
  docker exec <container_id> env
  ```
  Can also use `inspect` command to see the `ENV` variables, under `"config" -> "ENV"`

## Creating Your Own Docker Image

### Conceptualizing the Process
Imagine you want to deploy a Flask application. You'll need an environment that includes:
1. An Operating System (OS), e.g., Ubuntu.
2. Updated software repositories.
3. Necessary software dependencies.
4. Python and its dependencies.
5. Your source code.
6. Commands to run your Flask web server.

### Example Dockerfile for a Flask Application
Here's how you'd create a Dockerfile for a simple Flask application:

```dockerfile
# Use Ubuntu as the base image
FROM Ubuntu

# Update the apt package repository
RUN apt-get update

# Install Python
RUN apt-get install python

# Install Flask and Flask-MySQL using pip
RUN pip install flask
RUN pip install flask-mysql

# Copy the source code to the /opt folder in the container
COPY . /opt/source_code

# Set the command to run the Flask application
ENTRYPOINT FLASK_APP=/opt/source_code/app.py flask run
```

### Explanation of the Dockerfile
- `FROM Ubuntu`: This line sets the base image. In this case, we're starting with Ubuntu.
- `RUN apt-get update`: Updates the list of packages in the package manager.
- `RUN apt-get install python`: Installs Python.
- `RUN pip install flask` and `RUN pip install flask-mysql`: These commands use pip to install Flask and Flask-MySQL, which are Python dependencies.
- `COPY . /opt/source_code`: Copies the source code from the current directory on your host machine into the `/opt/source_code` directory inside the container.
- `ENTRYPOINT FLASK_APP=/opt/source_code/app.py flask run`: Sets the default command to start the Flask application.

### Building and Pushing the Image
- **Building the Image**: 
  To build an image from a Dockerfile, use the `docker build` command. Navigate to the directory containing the Dockerfile and run:
  ```
  docker build -t my-flask-app .
  ```
  This command builds the Docker image and tags it as `my-flask-app`.

- **Pushing to Docker Hub**:
  Once the image is built, you can push it to Docker Hub using `docker push`. First, log in to Docker Hub, then:
  ```
  docker push my-flask-app
  ```
  Replace `my-flask-app` with your Docker Hub username and image name.

### Docker's Layered Architecture
Each command in a Dockerfile creates a layer:
- `FROM` creates a layer from the Ubuntu Docker image.
- `RUN`, `COPY`, and `ENTRYPOINT` commands each create a new layer on top of the previous one.
- Docker caches layers to speed up subsequent builds. If a layer hasn't changed, Docker reuses it.

This layered approach ensures efficient image building and minimizes the size of the image by reusing common layers across different images.
