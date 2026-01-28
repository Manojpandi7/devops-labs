# Docker Basics

## Running Containers

### `docker run <image>`
```bash
docker run nginx
```
This command will run an instance of the nginx application on the Docker host using the nginx image. If the image is not available locally, it will pull the image from Docker Hub and create the container. For subsequent executions, the same image will be used.

---

## Listing Containers

### `docker ps`
```bash
docker ps
```
Lists all **running** containers. It provides basic information like container ID and container name. Random IDs and names will be generated.

### `docker ps -a`
```bash
docker ps -a
```
Lists all containers — both **running** and **exited/stopped**.

---

## Managing Containers

### `docker stop <container>`
```bash
docker stop "container_id"
```
Stops the running container.

### `docker rm <container>`
```bash
docker rm "container_id"
```
Removes the container permanently.

---

## Managing Images

### `docker images`
```bash
docker images
```
Lists all images available on the Docker host.

### `docker rmi <image>`
```bash
docker rmi "image_name"
```
Removes the image from the Docker host.

### `docker pull <image>`
```bash
docker pull "image_name"
```
Pulls the image from Docker Hub and keeps it on the Docker host, but does **not** run a container.

---

## Understanding Container Lifecycle

### Why Containers Exit Immediately
```bash
docker run ubuntu
```
Once this command is executed, it will run an instance of the Ubuntu image. However, when we run `docker ps`, we will not see the container. When we run `docker ps -a`, the container will be in an **exited** state.

> **Key Concept:** Unlike VMs, containers are not meant to host an operating system. They are only meant to run a specific task or process — such as hosting an application, web server, database, or simply carrying out some kind of computation. Once the task is complete, the container exits. **The container exists as long as the task inside it exists.**

### Running a Container with a Command
```bash
docker run ubuntu sleep 5
```
As soon as this command is executed, it runs the Ubuntu image and executes the command `sleep 5`. Once 5 seconds are complete, the container exits.

---

## Executing Commands in Running Containers

### `docker exec <container> <command>`
```bash
docker exec "container_name" cat /etc/hosts
```
Runs a command inside a **running** container. Useful for debugging or inspecting container state.
