# MERN TravelMemory – Docker Setup Guide

This document describes how to configure and run the TravelMemory MERN application using Docker containers, a custom Docker network, and a persistent MongoDB volume.

## Prerequisites

- Docker Desktop installed and running
- TravelMemory project containing `backend` and `fronten` directories

## 1. Create Docker Network

Create a custom network for communication between containers.

```bash
docker network create mern-network-b18a
```

Verify:

```bash
docker network ls
```

## 2. Create MongoDB Volume

Create a persistent volume to store MongoDB data.

```bash
docker volume create batch18amongo
```

Verify:

```bash
docker volume ls
```

## 3. Run MongoDB Container

### Option A: Without exposing the MongoDB port

Use this option when MongoDB is accessed only by containers on the Docker network.

```bash
docker run -d --name mongo --network mern-network-b18a -v batch18amongo:/data/db mongo
```

### Option B: Expose MongoDB on host port 27018

Use this option when you also need to access MongoDB from your host machine.

```bash
docker run -d -p 27018:27017 --name mongo --network mern-network-b18a -v batch18amongo:/data/db mongo
```

Run only one option. If a container named `mongo` already exists, remove it before recreating it:

```bash
docker rm -f mongo
```

Verify:

```bash
docker ps
```

## 4. Build and Run the Backend

Navigate to the backend directory:

```powershell
cd .\backend
```

Build the backend Docker image:

```bash
docker build . -t merntmbe:v1.0
```

Run the backend container with the MongoDB connection string and custom network:

```bash
docker run -d --name backend -p 3001:3001 -e MONGO_URI=mongodb://mongo:27017/travelmemory --network mern-network-b18a merntmbe:v1.0
```

Verify the backend container:

```bash
docker ps
```

View backend logs:

```bash
docker logs backend
```

**Backend URL:**

```text
http://localhost:3001/
```

## 5. Build and Run the Frontend

Navigate to the frontend directory from the project root:

```powershell
cd .\fronten
```

Build the frontend Docker image:

```bash
docker build . -t tmfe:v1.0
```

Verify Docker images:

```bash
docker images
```

Run the frontend container:

```bash
docker run -d --name frontend -p 3000:3000 -e REACT_APP_BACKEND_URL=http://192.168.29.241:3001 --network mern-network-b18a tmfe:v1.0
```

Replace `192.168.29.241` with your host machine's current IPv4 address if it changes.

**Frontend URL:**

```text
http://192.168.29.241:3000/
```

You can also access it using `http://localhost:3000/` from the same machine.

**Important:** If `REACT_APP_BACKEND_URL` is used by Create React App, its value is generally embedded during the image build. If your frontend uses this behavior, supply the variable during `docker build` or configure runtime environment-variable substitution in the frontend container. Also, the backend URL must be reachable from the browser, not just from another container.

## 6. Verify the Complete Setup

Check running containers:

```bash
docker ps -a
```

Check the network:

```bash
docker network inspect mern-network-b18a
```

Check the persistent volume:

```bash
docker volume inspect batch18amongo
```

View logs:

```bash
docker logs mongo
docker logs backend
docker logs frontend
```

## 7. Application Architecture

```text
                 Browser
                    |
                    v
             Frontend :3000
                    |
                    v
             Backend API :3001
                    |
                    v
              MongoDB :27017

        Docker Network: mern-network-b18a
        MongoDB Volume: batch18amongo
```

The frontend communicates with the backend API. The backend connects to MongoDB through the Docker network using the hostname `mongo`.

The named volume `batch18amongo` preserves MongoDB data even if the MongoDB container is removed and recreated.

## 8. Useful Docker Commands

| Operation | Command |
|---|---|
| List containers | `docker ps -a` |
| List images | `docker images` |
| Stop backend | `docker stop backend` |
| Start backend | `docker start backend` |
| View backend logs | `docker logs backend` |
| Remove backend container | `docker rm -f backend` |
| Remove backend image | `docker rmi merntmbe:v1.0` |
| Inspect Docker network | `docker network inspect mern-network-b18a` |

## 9. Cleanup

Remove the application containers:

```bash
docker rm -f frontend backend mongo
```

Remove the Docker network:

```bash
docker network rm mern-network-b18a
```

Optional: Remove the MongoDB volume:

```bash
docker volume rm batch18amongo
```

**Warning:** Removing the volume permanently deletes the MongoDB data stored in it. Keep the volume if you want to preserve your database.

