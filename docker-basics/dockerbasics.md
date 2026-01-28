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

---

## Attached & Detached Modes

### Attached Mode (Default)
```bash
docker run "image_name"
```
This will simply run the image. For example, if you start a web server, once you execute the command it will run the instance of the image and you will **not** get the prompt back to type the next command unless you press `Ctrl+C`. This is running in **attached mode**.

### Detached Mode
```bash
docker run -d "image_name"
```
Runs the instance of the image in **detached mode** (in the background). This will print out the container ID and return the prompt immediately.

### Re-attaching to a Container
```bash
docker attach "container_id"
```
You can later re-attach to a running container using the container ID. You only need the **first 3 characters** of the container ID to identify it.

---

## Image Tags (Versions)

### Running a Specific Version
```bash
docker run nginx
```
This will run the **latest** version of the image by default.

```bash
docker run nginx:1.1.4
```
To run a specific version, use the **tag** concept. You can find available tags (latest, oldest, or specific versions) for any image on [Docker Hub](https://hub.docker.com).

---

## Interactive Mode

### Understanding the Problem

If we run an application locally that requires user input:
```bash
./app.sh
```
```
Welcome! Please enter the name: Manoj
Hello and welcome Manoj
```

### Non-Interactive Mode (Default)
```bash
docker run simple-prompt-application
```
```
Hello and welcome
```
The prompt is **missing** and the user cannot input anything. By default, Docker does not listen to any input — even though the container is attached to the console, it doesn't have a terminal to read from. This is **non-interactive mode**.

### Interactive Mode (`-i`)
```bash
docker run -i simple-prompt-application
```
```
Manoj
Hello and welcome Manoj
```
The `-i` flag enables **interactive mode** (keeps STDIN open). Now you can type input, but the prompt is still missing because the application's prompt isn't displayed — we haven't attached to the container's terminal yet.

### Interactive Mode with Terminal (`-it`)
```bash
docker run -it simple-prompt-application
```
```
Welcome! Please enter the name: Manoj
Hello and welcome Manoj
```
The `-t` flag stands for **pseudo-terminal**. With the combination `-it`, we are attached to the terminal in **interactive mode** — now both the prompt and input work correctly.
