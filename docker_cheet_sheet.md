# Docker Commands Cheat Sheet for Beginners

## 1. Basic Docker Commands

| Command | Description |
|---|---|
| `docker --version` | Check Docker version |
| `docker version` | Display client and server versions |
| `docker info` | Display Docker system information |
| `docker --help` | Display available Docker commands |
| `docker COMMAND --help` | Get help for a specific command |
| `docker system df` | Check Docker disk usage |
| `docker events` | View Docker system events |

## 2. Docker Image Commands

An **image** is a template used to create containers.

| Command | Description |
|---|---|
| `docker images` | List downloaded images |
| `docker pull nginx` | Download an image |
| `docker search nginx` | Search for images on Docker Hub |
| `docker build -t myapp:v1 .` | Build an image from a Dockerfile |
| `docker image inspect nginx` | View image details |
| `docker history nginx` | View image layers |
| `docker tag myapp:v1 username/myapp:v1` | Assign a new image tag |
| `docker rmi myapp:v1` | Remove an image |
| `docker image prune` | Remove dangling images |
| `docker image prune -a` | Remove unused images |

## 3. Docker Container Commands

A **container** is an instance of an image.

| Command | Description |
|---|---|
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker run nginx` | Create and run a container |
| `docker run -d nginx` | Run a container in the background |
| `docker run --name web nginx` | Assign a container name |
| `docker start web` | Start a stopped container |
| `docker stop web` | Stop a running container |
| `docker restart web` | Restart a container |
| `docker kill web` | Force-stop a container |
| `docker rm web` | Remove a stopped container |
| `docker rm -f web` | Force-remove a container |
| `docker inspect web` | Display container configuration |
| `docker rename web web-new` | Rename a container |
| `docker port web` | Display published ports |
| `docker top web` | Display processes inside a container |
| `docker stats` | Monitor container resource usage |

## 4. Important `docker run` Options

| Option | Purpose | Example |
|---|---|---|
| `-d` | Run in the background | `docker run -d nginx` |
| `-it` | Interactive terminal | `docker run -it ubuntu sh` |
| `--name` | Assign a name | `--name mycontainer` |
| `-p` | Publish a port | `-p 8080:80` |
| `-e` | Set an environment variable | `-e PORT=3000` |
| `-v` | Mount a volume or bind mount | `-v mydata:/data` |
| `--network` | Attach to a network | `--network mynetwork` |
| `--rm` | Remove the container after it exits | `docker run --rm hello-world` |
| `--restart` | Configure restart behavior | `--restart unless-stopped` |

**Port mapping example:**

```bash
docker run -d --name web -p 8080:80 nginx
```

Here, host port `8080` forwards traffic to container port `80`.

## 5. Docker Logs and Debugging

| Command | Description |
|---|---|
| `docker logs web` | Display container logs |
| `docker logs -f web` | Follow logs continuously |
| `docker logs --tail 50 web` | Display the last 50 lines |
| `docker logs --since 10m web` | Display logs from the last 10 minutes |
| `docker exec -it web sh` | Open a shell in a running container |
| `docker exec -it web bash` | Open Bash if installed |
| `docker cp web:/app/file.txt ./file.txt` | Copy a file from container to host |
| `docker diff web` | Show filesystem changes |
| `docker inspect web` | Inspect detailed configuration |

Note: `docker exec` requires the container to be running. Minimal images may not include Bash, so try `sh` if Bash is unavailable.

## 6. Docker Network Commands

A **network** enables containers to communicate.

| Command | Description |
|---|---|
| `docker network ls` | List networks |
| `docker network create mynetwork` | Create a network |
| `docker network inspect mynetwork` | Inspect a network |
| `docker network connect mynetwork web` | Connect a container to a network |
| `docker network disconnect mynetwork web` | Disconnect a container |
| `docker network rm mynetwork` | Remove a network |
| `docker network prune` | Remove unused networks |

## 7. Docker Volume Commands

A **volume** stores persistent data outside a container's writable filesystem.

| Command | Description |
|---|---|
| `docker volume ls` | List volumes |
| `docker volume create mydata` | Create a volume |
| `docker volume inspect mydata` | Inspect a volume |
| `docker volume rm mydata` | Remove a volume |
| `docker volume prune` | Remove unused volumes |

Example:

```bash
docker volume create mydata
docker run -d --name db -v mydata:/data/db mongo
```

Removing a container does not normally remove its named volume. Removing the volume itself can permanently delete its stored data.

## 8. Docker Hub and Registry Commands

A registry stores and distributes Docker images. Docker Hub is a popular public registry.

| Command | Description |
|---|---|
| `docker login` | Sign in to a registry |
| `docker logout` | Sign out |
| `docker pull nginx` | Download an image |
| `docker push username/myapp:v1` | Upload an image |
| `docker tag myapp:v1 username/myapp:v1` | Tag an image for publishing |
| `docker search nginx` | Search Docker Hub |

Typical workflow:

```bash
docker login
docker tag myapp:v1 username/myapp:v1
docker push username/myapp:v1
```

Replace `username` with your Docker Hub username.

## 9. Docker Cleanup Commands

| Command | Description |
|---|---|
| `docker container prune` | Remove stopped containers |
| `docker image prune` | Remove dangling images |
| `docker image prune -a` | Remove unused images |
| `docker network prune` | Remove unused networks |
| `docker volume prune` | Remove unused volumes |
| `docker system prune` | Remove various unused Docker resources |
| `docker system prune -a` | More extensive cleanup of unused resources |
| `docker system df` | Check disk usage before cleanup |

**Warning:** Cleanup commands can remove resources you may want to keep. Review the confirmation prompt and understand what will be deleted before proceeding.

## 10. Docker Compose Commands

Docker Compose manages multiple related containers using a YAML configuration file.

| Command | Description |
|---|---|
| `docker compose version` | Check Compose version |
| `docker compose config` | Validate and display configuration |
| `docker compose build` | Build service images |
| `docker compose up` | Create and start services |
| `docker compose up -d` | Start services in the background |
| `docker compose ps` | List Compose containers |
| `docker compose logs` | Display service logs |
| `docker compose logs -f` | Follow service logs |
| `docker compose stop` | Stop services |
| `docker compose start` | Start existing services |
| `docker compose restart` | Restart services |
| `docker compose down` | Stop and remove project containers and networks |
| `docker compose pull` | Pull service images |

These commands generally require a Compose file in the current directory or a file specified with `-f`.

## 11. Dockerfile Instructions

These are instructions used when writing a Dockerfile; they are not commands typed directly into the terminal.

| Instruction | Purpose |
|---|---|
| `FROM` | Specify the base image |
| `WORKDIR` | Set the working directory |
| `COPY` | Copy files into the image |
| `ADD` | Copy files or extract supported archives |
| `RUN` | Execute commands during image building |
| `ENV` | Set environment variables |
| `ARG` | Define build-time variables |
| `EXPOSE` | Document the intended container port |
| `CMD` | Set the default command |
| `ENTRYPOINT` | Define the main executable |
| `USER` | Set the user for subsequent instructions and runtime |
| `VOLUME` | Declare a mount point |
| `HEALTHCHECK` | Configure a container health check |
| `.dockerignore` | Exclude files from the build context |

## 12. Essential Commands to Memorize

Start by learning these frequently used commands:

```bash
docker --version
docker pull nginx
docker images
docker build -t myapp:v1 .
docker run -d --name web -p 8080:80 nginx
docker ps
docker ps -a
docker logs -f web
docker exec -it web sh
docker stop web
docker start web
docker restart web
docker rm web
docker rmi myapp:v1
docker network ls
docker volume ls
docker inspect web
docker stats
docker system df
```

## 13. Important Concepts

| Concept | Meaning |
|---|---|
| Image | Template used to create containers |
| Container | Running or stopped instance of an image |
| Dockerfile | Instructions for building an image |
| Registry | Storage for Docker images |
| Volume | Persistent data storage |
| Network | Communication between containers |
| Port mapping | Connects a host port to a container port |
| Environment variable | Supplies configuration to an application |
| Docker Compose | Defines and manages multiple services |
| `.dockerignore` | Excludes files from the Docker build context |

## Official Documentation

- [Docker CLI Cheat Sheet](https://docs.docker.com/get-started/docker_cheatsheet.pdf)
- [Docker CLI Reference](https://docs.docker.com/reference/cli/docker/)
- [Dockerfile Reference](https://docs.docker.com/reference/dockerfile/)
- [Docker Compose Reference](https://docs.docker.com/reference/cli/docker/compose/)

