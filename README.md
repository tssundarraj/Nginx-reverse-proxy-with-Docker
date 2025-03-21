# FastAPI with NGINX Reverse Proxy in Docker

This project sets up a **FastAPI** application running behind an **NGINX reverse proxy** using Docker and Docker Compose.

---

## 📖 Index
1. [Features](#1️⃣-features)
2. [Prerequisites](#2️⃣-prerequisites)
3. [Why Use a Reverse Proxy?](#3️⃣-why-use-a-reverse-proxy)
4. [Problems Solved by a Reverse Proxy](#4️⃣-problems-solved-by-a-reverse-proxy)
5. [Problem Solved by Docker Compose vs Docker](#5️⃣-problem-solved-by-docker-compose-vs-docker)
6. [Project Structure](#6️⃣-project-structure)
7. [Setup Instructions](#7️⃣-setup-instructions)
    - [Step 1: Clone the Repository](#step-1-clone-the-repository)
    - [Step 2: Create the FastAPI Application](#step-2-create-the-fastapi-application)
    - [Step 3: Setup NGINX Reverse Proxy](#step-3-setup-nginx-reverse-proxy)
    - [Step 4: Create docker-compose.yml](#step-4-create-docker-composeyml)
    - [Step 5: Build and Run the Containers](#step-5-build-and-run-the-containers)
    - [Step 6: Add Entry in /etc/hosts](#step-6-add-entry-in-etchosts)
8. [Accessing Swagger UI](#8️⃣-accessing-swagger-ui)
9. [Testing the Setup](#9️⃣-testing-the-setup)
10. [Stopping and Cleaning Up](#🔟-summary)
11. [Summary](#🔟-summary)
12. [Next Steps](#next-steps)
13. [Dockerfile Explanation](#dockerfile)
14. [Docker Compose File Explanation](#docker-compose-file)
15. [Author](#🔟-author)
16. [Thank You](#🙏-thank-you)

---

## 1️⃣ Features
- **FastAPI** backend running in a Docker container.
- **NGINX** as a reverse proxy to serve FastAPI.
- **Docker Compose** for easy deployment and management.
- **Swagger UI** for API documentation and testing.

## 2️⃣ Prerequisites
- Docker & Docker Compose installed
- Basic knowledge of FastAPI, NGINX, and Docker

## 3️⃣ Why Use a Reverse Proxy?
A **reverse proxy** like NGINX is used in this project to:
- **Improve Security**: Protects the backend by hiding internal infrastructure.
- **Load Balancing**: If multiple FastAPI instances are running, NGINX can distribute traffic.
- **SSL Termination**: Easier to handle HTTPS at the proxy level.
- **Performance Optimization**: Caching and compression features can improve response times.
- **Custom Domain Handling**: Allows mapping FastAPI to a friendly domain instead of an IP and port.

## 4️⃣ Problems Solved by a Reverse Proxy
- **Direct Access Prevention**: Without a reverse proxy, the FastAPI service would be directly exposed to users, which is not ideal for security and scalability.
- **Multiple Services Management**: If additional microservices are added in the future, NGINX can route traffic accordingly.
- **Single Entry Point**: Instead of accessing different services with different ports, users access everything through a single domain (e.g., `http://fastapi.local`).
- **Efficient Resource Usage**: Offloading static file handling and request routing to NGINX allows FastAPI to focus on processing API requests.

## 5️⃣ Problem Solved by Docker Compose vs Docker
Instead of running separate `docker run` commands, **Docker Compose** simplifies the process by managing multi-container applications with a single configuration file.

## 6️⃣ Project Structure
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

## 7️⃣ Setup Instructions

### Step 1: Clone the Repository
```sh
git clone https://github.com/your-username/fastapi-nginx-docker.git
cd fastapi-nginx-docker
```

### Step 2: Create the FastAPI Application
Inside `app/` create `main.py`:
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

## Step 3: Setup NGINX Reverse Proxy
Create an NGINX configuration file `nginx.conf` inside the `nginx/` directory.

```nginx
server {
    listen 80; # Listen on port 80
    server_name localhost; # Define the server name

    location / {
        proxy_pass http://fastapi:8000; # Forward requests to the FastAPI container
        proxy_set_header Host $host; # Preserve the host header
        proxy_set_header X-Real-IP $remote_addr; # Forward the real IP address
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; # Forward client information
    }
}
```

## Step 4: Create `docker-compose.yml`
Create a `docker-compose.yml` file to define the services.

```yaml
version: '3.8' # Use Docker Compose version 3.8

services:
  fastapi:
    build: ./app # Build FastAPI from the app directory
    container_name: fastapi_app # Name the container
    ports:
      - "8000:8000" # Expose FastAPI on port 8000

  nginx:
    image: nginx:latest # Use the latest NGINX image
    container_name: nginx_proxy # Name the NGINX container
    ports:
      - "80:80" # Expose NGINX on port 80
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro # Mount the NGINX configuration file
    depends_on:
      - fastapi # Ensure NGINX starts after FastAPI
```

## Step 5: Build and Run the Containers
Run the following command to build and start the containers:

```sh
docker-compose up --build -d # Build and run containers in detached mode
```

## Step 6: Add Entry in /etc/hosts
Modify the `/etc/hosts` file to map `localhost` to the local environment:

```sh
echo "127.0.0.1 fastapi.local" | sudo tee -a /etc/hosts # Add entry to /etc/hosts
```

This will allow you to access the FastAPI app via `http://fastapi.local` instead of using an IP address.
## 8️⃣ Accessing Swagger UI
FastAPI automatically provides an interactive API documentation using **Swagger UI**.

- Open your browser and visit:
  ```
  http://fastapi.local/docs
  ```
  or
  ```
  http://127.0.0.1/docs
  ```
- This will open the **Swagger UI**, where you can test your API endpoints directly.

For **Redoc UI**, visit:
```
http://fastapi.local/redoc
```

## 9️⃣ Testing the Setup
Open your browser and visit:
```
http://fastapi.local
```
Or use `curl`:
```sh
curl http://fastapi.local
```

## 🔟 Summary
- FastAPI serves the API on port **8000**
- NGINX acts as a reverse proxy on port **80**
- Docker Compose manages both containers
- Swagger UI is accessible at `/docs`

## Next Steps
Now that the basic setup is complete, here are some ideas for expanding this project:
- **Implement Authentication**: Add JWT or OAuth2-based authentication for securing endpoints.
- **Database Integration**: Connect FastAPI to PostgreSQL, MySQL, or MongoDB.
- **Logging & Monitoring**: Use tools like Prometheus, Grafana, or ELK Stack to monitor requests and errors.
- **Deploy to the Cloud**: Deploy the setup to AWS, Azure, or DigitalOcean.
- **Load Balancing & Scaling**: Run multiple instances of FastAPI and use NGINX for load balancing.
- **Automate Deployment**: Use CI/CD tools like GitHub Actions or Jenkins for automated deployment.

## 🔟 Author
[T S Sundar Raj]

## 🙏 Thank You!
Happy coding! 🎉

