# FastAPI with NGINX Reverse Proxy in Docker

This project sets up a **FastAPI** application running behind an **NGINX reverse proxy** using Docker and Docker Compose.

---

## 📖 Table of Contents

1. [Features](#features)
2. [Prerequisites](#prerequisites)
3. [Why Use a Reverse Proxy?](#why-use-a-reverse-proxy)
4. [Project Structure](#project-structure)
5. [Setup Instructions](#setup-instructions)
    - [Step 1: Clone the Repository](#step-1-clone-the-repository)
    - [Step 2: Create the FastAPI Application](#step-2-create-the-fastapi-application)
    - [Step 3: Setup NGINX Reverse Proxy](#step-3-setup-nginx-reverse-proxy)
    - [Step 4: Create the Dockerfile](#step-4-create-the-dockerfile)
    - [Step 5: Create docker-compose.yml](#step-5-create-docker-composeyml)
    - [Step 6: Build and Run the Containers](#step-6-build-and-run-the-containers)
    - [Step 7: Add Entry in /etc/hosts](#step-7-add-entry-in-etc-hosts)
6. [Accessing Swagger UI](#accessing-swagger-ui)
7. [Testing the Setup](#testing-the-setup)
8. [Summary](#summary)
9. [Next Steps](#next-steps)
10. [Author](#author)
11. [Thank You](#thank-you)

---

## 🚀 Features
- **FastAPI** backend running in a Docker container.
- **NGINX** as a reverse proxy to serve FastAPI.
- **Docker Compose** for easy deployment and management.
- **Swagger UI** for API documentation and testing.

## 📌 Prerequisites
- Docker & Docker Compose installed.
- Basic knowledge of FastAPI, NGINX, and Docker.

## 🌍 Why Use a Reverse Proxy?
A **reverse proxy** like NGINX is used in this project to:
- **Improve Security**: Protects the backend by hiding internal infrastructure.
- **Load Balancing**: Distributes traffic when multiple FastAPI instances are running.
- **SSL Termination**: Handles HTTPS at the proxy level.
- **Performance Optimization**: Caching and compression features can improve response times.
- **Custom Domain Handling**: Allows mapping FastAPI to a friendly domain instead of an IP and port.

## 📂 Project Structure
```
fastapi-nginx-docker/
│── app/
│   ├── main.py
│   ├── Dockerfile
│── nginx/
│   ├── nginx.conf
│── docker-compose.yml
│── README.md
```

## 🛠️ Setup Instructions

### Step 1: Clone the Repository
```sh
git clone https://github.com/your-username/fastapi-nginx-docker.git
cd fastapi-nginx-docker
```

### Step 2: Create the FastAPI Application
Inside `app/`, create `main.py`:
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello from FastAPI!"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "query": q}
```

### Step 3: Setup NGINX Reverse Proxy
Create an NGINX configuration file `nginx.conf` inside the `nginx/` directory.
```nginx
server {
    listen 80;
    server_name localhost;

    location / {
        proxy_pass http://fastapi:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

### Step 4: Create the Dockerfile
Inside `app/`, create `Dockerfile`:
```dockerfile
# Use official Python image
FROM python:3.9

# Set working directory
WORKDIR /app

# Copy FastAPI app files
COPY . .

# Install dependencies
RUN pip install --no-cache-dir fastapi uvicorn

# Expose FastAPI port
EXPOSE 8000

# Run FastAPI server
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Step 5: Create `docker-compose.yml`
Create a `docker-compose.yml` file to define the services.
```yaml
version: '3.8'

services:
  fastapi:
    build: ./app
    container_name: fastapi_app
    ports:
      - "8000:8000"

  nginx:
    image: nginx:latest
    container_name: nginx_proxy
    ports:
      - "80:80"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - fastapi
```

### Step 6: Build and Run the Containers
Run the following command to build and start the containers:
```sh
docker-compose up --build -d
```

### Step 7: Add Entry in `/etc/hosts`
Modify the `/etc/hosts` file to map `localhost` to the local environment:
```sh
echo "127.0.0.1 fastapi.local" | sudo tee -a /etc/hosts
```
This will allow you to access the FastAPI app via `http://fastapi.local` instead of using an IP address.

---

## 🌐 Accessing Swagger UI
FastAPI automatically provides interactive API documentation using **Swagger UI**.
- Open your browser and visit:
  ```
  http://fastapi.local/docs
  ```
  or
  ```
  http://127.0.0.1/docs
  ```
- For **Redoc UI**, visit:
  ```
  http://fastapi.local/redoc
  ```

## 🔎 Testing the Setup
Open your browser and visit:
```
http://fastapi.local
```
Or use `curl`:
```sh
curl http://fastapi.local
```

---

## 📌 Summary
- FastAPI serves the API on port **8000**.
- NGINX acts as a reverse proxy on port **80**.
- Docker Compose manages both containers.
- Swagger UI is accessible at `/docs`.

## 🚀 Next Steps
Here are some ideas for expanding this project:
- **Implement Authentication**: Add JWT or OAuth2-based authentication.
- **Database Integration**: Connect FastAPI to PostgreSQL, MySQL, or MongoDB.
- **Logging & Monitoring**: Use Prometheus, Grafana, or ELK Stack.
- **Deploy to the Cloud**: Deploy the setup to AWS, Azure, or DigitalOcean.
- **Load Balancing & Scaling**: Run multiple instances of FastAPI and use NGINX for load balancing.
- **Automate Deployment**: Use CI/CD tools like GitHub Actions or Jenkins.

## 👨‍💻 Author
[T S Sundar Raj]

## 🙏 Thank You!
Happy coding! 🎉

