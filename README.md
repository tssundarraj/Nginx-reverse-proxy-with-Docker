# 📌 FastAPI with NGINX Reverse Proxy in Docker

This project sets up a **FastAPI** application running behind an **NGINX reverse proxy** using **Docker and Docker Compose**.

---

## 📖 Index
1. [Features](#features)
2. [Prerequisites](#prerequisites)
3. [Problem Solved by Docker Compose vs Docker](#problem-solved-by-docker-compose-vs-docker)
4. [Project Structure](#project-structure)
5. [Setup Instructions](#setup-instructions)
   - [Step 1: Clone the Repository](#step-1-clone-the-repository-or-create-the-directory)
   - [Step 2: Create the FastAPI Application](#step-2-create-the-fastapi-application)
   - [Step 3: Setup NGINX Reverse Proxy](#step-3-setup-nginx-reverse-proxy)
   - [Step 4: Create docker-compose.yml](#step-4-create-docker-composeyml)
   - [Step 5: Add Entry in /etc/hosts](#step-5-add-entry-in-etchosts)
   - [Step 6: Build and Run the Containers](#step-6-build-and-run-the-containers)
6. [Testing the Setup](#testing-the-setup)
7. [Stopping and Cleaning Up](#stopping-and-cleaning-up)
8. [Summary](#summary)
9. [Docker Compose File Explanation](#docker-compose-file-explanation)
10. [Author](#author)
11. [Thank You](#thank-you)

---

## 🚀 Features
- ✅ FastAPI application running on port **8000** internally
- ✅ NGINX reverse proxy routing requests from **port 80** to FastAPI
- ✅ Swagger UI available at `http://localhost/docs`
- ✅ Dockerized setup for easy deployment

---

## ⚙️ Prerequisites
Ensure you have the following installed:

- **Docker**
- **Docker Compose**

To install Docker and Docker Compose:

```bash
sudo apt update
sudo apt install docker.io docker-compose -y
sudo systemctl enable --now docker
```

---

## 🔄 Problem Solved by Docker Compose vs Docker
### 🐳 Using Docker (Without Compose)
If you use Docker alone, you need to manually start and link containers. For example:
```bash
docker run -d --name fastapi_app -p 8000:8000 fastapi_image
```
Then, you need to start NGINX separately and configure networking manually.

### ⚡ Using Docker Compose
Docker Compose makes it easier to manage multiple services. It:
- Starts all services together (`fastapi_app` and `nginx`)
- Manages dependencies automatically (ensures FastAPI starts before NGINX)
- Uses a single command to bring up or down the whole stack

With `docker-compose up -d`, everything is set up automatically.

---

## 📂 Project Structure
```
nginx-fastapi/
│── fastapi_app/
│   ├── main.py
│   ├── requirements.txt
│   ├── Dockerfile
│
│── nginx/
│   ├── nginx.conf
│
│── docker-compose.yml
│── README.md
```

---

## 🛠️ Setup Instructions

### 1️⃣ Step 1: Clone the Repository (or Create the Directory)
```bash
mkdir nginx-fastapi && cd nginx-fastapi
```

### 2️⃣ Step 2: Create the FastAPI Application
```bash
mkdir fastapi_app && cd fastapi_app
```

#### Create `main.py`
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello from FastAPI!"}
```

#### Create `requirements.txt`
```
fastapi
uvicorn
```

#### Create `Dockerfile`
```dockerfile
FROM python:3.9

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Go back to the root directory:
```bash
cd ..
```

---

### 3️⃣ Step 3: Setup NGINX Reverse Proxy
```bash
mkdir nginx && cd nginx
```

#### Create `nginx.conf`
```nginx
events {}

http {
    server {
        listen 80;

        location / {
            proxy_pass http://fastapi_app:8000/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

Go back to the root directory:
```bash
cd ..
```

---

### 4️⃣ Step 4: Create `docker-compose.yml`
```yaml
version: "3.8"

services:
  fastapi_app:
    build: ./fastapi_app  # Builds the FastAPI application from the specified directory
    container_name: fastapi_app  # Names the container as "fastapi_app"
  
  nginx:
    image: nginx:latest  # Uses the latest NGINX image from Docker Hub
    container_name: nginx_reverse_proxy  # Names the container as "nginx_reverse_proxy"
    ports:
      - "80:80"  # Maps port 80 on the host to port 80 in the container
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro  # Mounts NGINX configuration file in read-only mode
    depends_on:
      - fastapi_app  # Ensures NGINX starts only after FastAPI is running
```

---

## ✅ Testing the Setup
- Open FastAPI: `curl http://localhost`
- Open Swagger UI:
  - [http://localhost/docs](http://localhost/docs)
  - [http://fastapi.local/docs](http://fastapi.local/docs)

---

## 🛑 Stopping and Cleaning Up
```bash
docker-compose down
docker system prune -a
```

---

## 🎯 Summary
✔ **FastAPI** runs on port **8000** internally.  
✔ **NGINX** reverse proxies traffic from **port 80** to FastAPI.  
✔ **Swagger UI** available at `http://localhost/docs`.  
✔ **Docker Compose** simplifies deployment.  
✔ **Custom domain access** via `/etc/hosts` is possible.  
✔ Everything runs inside **Docker** containers. 🚀  

---

## ✍️ Author
**T S Sundar Raj**
---
 **🙏 Thank You!**


