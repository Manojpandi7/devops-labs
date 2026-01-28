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

---

## Port Mapping

### The Problem
```bash
docker run simple-web-app
```
```
Running on http://0.0.0.0:5000
```

The container runs on port **5000** and has an internal IP like `172.17.0.2`. This IP is only accessible **within the Docker host**. Users outside cannot access the application.

```
┌─────────────────────────────────────────────────────────────────┐
│  DOCKER HOST (192.168.1.5)                                      │
│                                                                 │
│    ┌─────────────────────┐                                      │
│    │  Container          │                                      │
│    │  IP: 172.17.0.2     │    ← Only accessible inside host     │
│    │  Port: 5000         │                                      │
│    └─────────────────────┘                                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
        ↑
        ✗ External users CANNOT access 172.17.0.2:5000
```

### The Solution: Port Mapping
```bash
docker run -p 80:5000 simple-web-app
```

Map a port on the **Docker host** to a port on the **container**:

```
┌─────────────────────────────────────────────────────────────────┐
│  DOCKER HOST (192.168.1.5)                                      │
│                                                                 │
│    Port 80 ──────────────────┐                                  │
│                              │                                  │
│                              ▼                                  │
│                    ┌─────────────────────┐                      │
│                    │  Container          │                      │
│                    │  IP: 172.17.0.2     │                      │
│                    │  Port: 5000         │                      │
│                    └─────────────────────┘                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
        ↑
        ✓ External users access http://192.168.1.5:80
```

### Syntax
```bash
docker run -p <host_port>:<container_port> <image>
```

### Multiple Port Mappings
You can map multiple ports to different containers:
```bash
docker run -p 80:5000 webapp1
docker run -p 8080:5000 webapp2
docker run -p 3306:3306 mysql
```

> ⚠️ **Note:** You cannot map the **same host port** more than once.

---

## Volume Mapping (Data Persistence)

### The Problem
```bash
docker run mysql
```

Data inside the container (e.g., `/var/lib/mysql`) is stored **inside the container**. If the container is deleted, **all data is lost**.

```
┌─────────────────────────────────────────────────────────────────┐
│  DOCKER HOST                                                    │
│                                                                 │
│    ┌─────────────────────┐                                      │
│    │  MySQL Container    │                                      │
│    │                     │                                      │
│    │  /var/lib/mysql ────┼──── Data lives HERE (inside)         │
│    │                     │                                      │
│    └─────────────────────┘                                      │
│              ↓                                                  │
│         Container deleted = Data GONE! ✗                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### The Solution: Volume Mapping
```bash
docker run -v /opt/datadir:/var/lib/mysql mysql
```

Map a directory on the **host** to a directory inside the **container**:

```
┌─────────────────────────────────────────────────────────────────┐
│  DOCKER HOST                                                    │
│                                                                 │
│    /opt/datadir ◄────────────────────┐                          │
│    (Data persists here!) ✓           │ Volume Mount             |
│                                      │                          │
│    ┌─────────────────────┐           │                          │
│    │  MySQL Container    │           │                          │
│    │                     │           │                          │
│    │  /var/lib/mysql ────┼───────────┘                          │
│    │                     │                                      │
│    └─────────────────────┘                                      │
│              ↓                                                  │
│         Container deleted = Data SAFE! ✓                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Syntax
```bash
docker run -v <host_path>:<container_path> <image>
```

---

## Inspecting Containers

### `docker inspect`
```bash
docker inspect "container_name"
```
Returns detailed configuration and state information about a container in **JSON format** (network settings, mounts, environment variables, etc.).

### `docker logs`
```bash
docker logs "container_name"
```
Displays the **logs** (stdout/stderr output) of a container.