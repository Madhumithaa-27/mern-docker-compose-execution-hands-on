

---

# MERN Stack Application with Docker

## Overview

This is a simple MERN (MongoDB, Express, React, Node.js) stack application containerized using Docker. The project demonstrates how to build, run, and connect multiple services (frontend, backend, and database) using Docker containers and Docker networking.

---

## Docker Concepts Used

* Containerization of frontend, backend, and database
* Docker networking for service communication
* Port mapping between host and containers
* Persistent storage using volumes
* Docker Compose for multi-container orchestration

---

## Project Architecture

Frontend (React + Vite)
|
v
Backend (Node.js + Express)
|
v
MongoDB Database

All services communicate through a Docker network.

---

## Step-by-Step Setup (Docker CLI)

### 1. Create Docker Network

```bash
docker network create demo
```

This creates a bridge network so containers can communicate using their container names.

---

## 2. Build the Client (Frontend)

```bash
cd mern/frontend
docker build -t mern-frontend .
```

---

## 3. Run the Client

```bash
docker run --name=frontend --network=demo -d -p 5173:5173 mern-frontend
```

---

## 4. Verify Frontend is Running

Open browser:

```text
http://localhost:5173
```

---

## 5. Run MongoDB Container

```bash
docker run --network=demo --name mongodb -d -p 27017:27017 -v ~/opt/data:/data/db mongo:latest
```

This ensures MongoDB data persistence using volumes.

---

## 6. Build the Backend Server

```bash
cd mern/backend
docker build -t mern-backend .
```

---

## 7. Run the Backend Server

```bash
docker run --name=backend --network=demo -d -p 5050:5050 mern-backend
```

---

## Using Docker Compose

Instead of running multiple commands manually, Docker Compose can be used:

```bash
docker compose up -d
```

This automatically:

* Builds images
* Creates containers
* Sets up networking
* Starts all services

---

## Docker Compose Configuration

```yaml
services:
  backend:
    build: ./mern/backend
    ports:
      - "5050:5050"
    networks:
      - mern_network
    environment:
      MONGO_URI: mongodb://mongodb:27017/mydatabase
    depends_on:
      - mongodb

  frontend:
    build: ./mern/frontend
    ports:
      - "5173:5173"
    networks:
      - mern_network
    environment:
      VITE_API_URL: http://backend:5050

  mongodb:
    image: mongo:latest
    ports:
      - "27017:27017"
    networks:
      - mern_network
    volumes:
      - mongo-data:/data/db

networks:
  mern_network:
    driver: bridge

volumes:
  mongo-data:
```

---

## Key Concepts Learned

### Docker Networking

All containers are connected using a custom network (`demo` or `mern_network`). This allows communication using container names instead of localhost.

Examples:

* backend connects to MongoDB using `mongodb://mongodb:27017`
* frontend connects to backend using `http://backend:5050`

---

### Port Mapping

| Service  | Container Port | Host Port |
| -------- | -------------- | --------- |
| Frontend | 5173           | 5173      |
| Backend  | 5050           | 5050      |
| MongoDB  | 27017          | 27017     |

---

### Volumes

MongoDB uses a volume to persist data:

```text
~/opt/data:/data/db
```

This ensures data is not lost when the container stops or restarts.

---

## Errors Faced and Fixes

### 1. Port Already in Use

Problem: Port 5173 already allocated

Fix:

* Stopped existing frontend container before running new one

---

### 2. Container Name Conflict

Problem: Container name already exists

Fix:

* Removed existing container using `docker rm -f`

---

### 3. MongoDB Connection Issue

Problem: Backend could not connect to MongoDB

Fix:

* Used correct Docker service name in URI:
  `mongodb://mongodb:27017`

---

### 4. Module Not Found Error

Problem: `Cannot find module /app/server.js`

Fix:

* Corrected backend start script in package.json

---

### 5. Network Issues

Problem: Services could not communicate

Fix:

* Ensured all containers are connected to same Docker network

---

## Access Application

* Frontend: [http://localhost:5173](http://localhost:5173)
* Backend: [http://localhost:5050](http://localhost:5050)
* MongoDB: localhost:27017

## output scrrenshots:
<img width="1365" height="642" alt="Screenshot 2026-04-16 202723" src="https://github.com/user-attachments/assets/5e2b36d2-c51f-4155-bd81-6a8880d68a0e" />

<img width="1365" height="707" alt="Screenshot 2026-04-16 205708" src="https://github.com/user-attachments/assets/bab0bf0d-bfb2-496c-9170-3e46504e817b" />

<img width="1365" height="698" alt="Screenshot 2026-04-16 205902" src="https://github.com/user-attachments/assets/4daade37-6c11-476a-a145-7bb7db83d101" />

<img width="1346" height="695" alt="Screenshot 2026-04-16 210044" src="https://github.com/user-attachments/assets/eb6294d4-8067-47ea-a779-65483e9b6208" />

<img width="1362" height="671" alt="Screenshot 2026-04-16 210308" src="https://github.com/user-attachments/assets/c5252ffd-3008-4954-8fcf-24a6a2de3841" />





---

## Conclusion

This project demonstrates how to containerize a full MERN stack application using Docker. It covers image building, container execution, networking, volume management, and debugging real-world Docker issues.

---
