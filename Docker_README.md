# MERN TravelMemory – Complete Docker Setup Guide

This guide explains how to build and run the TravelMemory MERN application using Docker images and containers, a custom network, and a persistent MongoDB volume.

## 1. Prerequisites

- Docker Desktop installed and running.
- TravelMemory project with `backend` and `fronten` directories.
- Node.js application dependencies defined in each directory's `package.json`.

## 2. Project Structure

```text
TravelMemory/
├── backend/
│   ├── Dockerfile
│   ├── .dockerignore
│   ├── package.json
│   └── index.js
│
├── fronten/
│   ├── Dockerfile
│   ├── .dockerignore
│   ├── package.json
│   └── src/
│
└── README.md
```

## 3. Create a Docker Network

Open a terminal in the project root and execute:

```bash
docker network create mern-network-b18a
```

Verify the network:

```bash
docker network ls
```

The custom network allows the backend to communicate with MongoDB using the container name `mongo`.

## 4. Create a MongoDB Volume

Create persistent storage for MongoDB:

```bash
docker volume create batch18amongo
```

Verify the volume:

```bash
docker volume ls
```

## 5. Run the MongoDB Container

### Option A: Without exposing the MongoDB port

```bash
docker run -d --name mongo --network mern-network-b18a -v batch18amongo:/data/db mongo
```

### Option B: Expose MongoDB on host port 27018

```bash
docker run -d -p 27018:27017 --name mongo --network mern-network-b18a -v batch18amongo:/data/db mongo
```

Choose only one option. Option A is sufficient for communication between the backend and MongoDB containers.

Verify:

```bash
docker ps
```

MongoDB connection string for the backend:

```text
mongodb://mongo:27017/travelmemory
```

If the container already exists and you want to recreate it:

```bash
docker rm -f mongo
```

## 6. Create the Backend Dockerfile

Navigate to the backend directory:

```powershell
cd .\backend
```

Create a file named `Dockerfile` without any file extension.

Use the following configuration:

```dockerfile
FROM node:26-alpine3.23

WORKDIR /app

COPY . .

RUN npm install

ENV MONGO_URI=mongodb://mongo:27017/travelmemory
ENV PORT=3001

EXPOSE 3001

CMD ["node", "index.js"]
```

**Configuration details:**

- `FROM` selects the Node.js base image.
- `WORKDIR` sets `/app` as the working directory.
- `COPY` copies the backend source files.
- `RUN npm install` installs application dependencies.
- `MONGO_URI` specifies the MongoDB container hostname.
- `PORT` configures the backend port.
- `EXPOSE` documents the container port.
- `CMD` starts the Node.js application.

The `ENV MONGO_URI` value can also be overridden when running the container.

## 7. Create the Backend .dockerignore File

Inside the `backend` directory, create `.dockerignore`:

```text
.env
node_modules
npm-debug.log
```

This prevents local environment secrets and unnecessary dependencies from being copied into the Docker build context.

## 8. Build the Backend Docker Image

From the `backend` directory, execute:

```bash
docker build -t merntmbe:v1.0 .
```

Verify the image:

```bash
docker images
```

The backend image name remains **`merntmbe:v1.0`**.

## 9. Run the Backend Container

Run the backend on port `3001`, attach it to the custom network, and pass the MongoDB connection string:

```bash
docker run -d --name backend -p 3001:3001 -e MONGO_URI=mongodb://mongo:27017/travelmemory --network mern-network-b18a merntmbe:v1.0
```

Verify the container:

```bash
docker ps -a
```

View logs:

```bash
docker logs backend
```

Backend URL:

```text
http://localhost:3001
```

From another computer, use the host machine's reachable IPv4 address:

```text
http://192.168.29.241:3001
```

Replace the example IP with your actual host IP.

## 10. Create the Frontend Dockerfile

Return to the project root and navigate to the frontend directory:

```powershell
cd ..
cd .\fronten
```

Create a file named `Dockerfile` with this configuration:

```dockerfile
FROM node:26-alpine3.23

WORKDIR /app

COPY . .

RUN npm install

ENV REACT_APP_BACKEND_URL=http://localhost:3001

EXPOSE 3000

CMD ["npm", "start"]
```

**Configuration details:**

- `FROM` uses the Node.js Alpine image.
- `WORKDIR` sets the working directory.
- `COPY` copies the frontend source files.
- `RUN npm install` installs frontend dependencies.
- `REACT_APP_BACKEND_URL` configures the backend API URL.
- `EXPOSE 3000` documents the frontend port.
- `CMD` starts the frontend development server.

The `localhost` default is suitable only when the browser can access the backend on the same computer. For access from another computer, use the host machine's reachable IP address.

## 11. Create the Frontend .dockerignore File

Inside the `fronten` directory, create `.dockerignore`:

```text
.env
node_modules
npm-debug.log
build
```

This excludes local environment files, dependencies, logs, and previously generated build output from the Docker build context.

## 12. Build the Frontend Docker Image

From the `fronten` directory:

```bash
docker build -t tmfe:v1.0 .
```

Verify the images:

```bash
docker images
```

## 13. Run the Frontend Container

Run the frontend and configure its backend API URL:

```bash
docker run -d --name frontend -p 3000:3000 -e REACT_APP_BACKEND_URL=http://192.168.29.241:3001 --network mern-network-b18a tmfe:v1.0
```

Replace `192.168.29.241` with your actual host machine IPv4 address if it differs.

Verify:

```bash
docker ps -a
```

View frontend logs:

```bash
docker logs frontend
```

Frontend URL:

```text
http://192.168.29.241:3000
```

You can also use `http://localhost:3000` in a browser running on the Docker host.

**Important:** If your frontend uses Create React App, `REACT_APP_*` variables are generally read when the development server starts or when the application is built. Your supplied `CMD ["npm", "start"]` starts the development server, so the `docker run -e` override can work. The backend URL must be reachable from the browser, and the backend must allow requests from the frontend origin through its CORS configuration.

## 14. Verify the Complete Application

Check all containers:

```bash
docker ps -a
```

Check the network:

```bash
docker network inspect mern-network-b18a
```

Check the volume:

```bash
docker volume inspect batch18amongo
```

Check logs:

```bash
docker logs mongo
docker logs backend
docker logs frontend
```

Open the frontend in your browser:

```text
http://localhost:3000
```

Confirm that the frontend loads, API requests succeed, and MongoDB data is accessible.

## 15. Application Architecture

```text
              User Browser
                   |
                   v
           Frontend :3000
                   |
                   v
           Backend API :3001
                   |
                   v
            MongoDB :27017

       Docker Network:
       mern-network-b18a

       Persistent Volume:
       batch18amongo
```

The frontend sends API requests to the backend. The backend connects to MongoDB through the Docker network using `mongo` as the hostname. The named volume preserves database files independently of the MongoDB container lifecycle.

## 16. Useful Docker Commands

| Operation | Command |
|---|---|
| List containers | `docker ps -a` |
| List images | `docker images` |
| List networks | `docker network ls` |
| List volumes | `docker volume ls` |
| Stop backend | `docker stop backend` |
| Start backend | `docker start backend` |
| View backend logs | `docker logs backend` |
| Remove backend container | `docker rm -f backend` |
| Remove backend image | `docker rmi merntmbe:v1.0` |
| Inspect network | `docker network inspect mern-network-b18a` |

## 17. Cleanup

Remove the containers:

```bash
docker rm -f frontend backend mongo
```

Remove the network:

```bash
docker network rm mern-network-b18a
```

To remove the MongoDB volume, only when its data is no longer needed:

```bash
docker volume rm batch18amongo
```

**Warning:** Removing the volume permanently deletes the MongoDB data stored in it.

---

## Final Configuration Summary

| Component | Configuration |
|---|---|
| Docker network | `mern-network-b18a` |
| MongoDB container | `mongo` |
| MongoDB volume | `batch18amongo` |
| MongoDB internal port | `27017` |
| MongoDB optional host port | `27018` |
| Backend image | `merntmbe:v1.0` |
| Backend container | `backend` |
| Backend port | `3001` |
| Frontend image | `tmfe:v1.0` |
| Frontend container | `frontend` |
| Frontend port | `3000` |
| Frontend URL | `http://localhost:3000` |
| Backend URL | `http://localhost:3001` |

