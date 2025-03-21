# FastAPI with NGINX Reverse Proxy in Docker

This project sets up a **FastAPI** application running behind an **NGINX reverse proxy** using Docker and Docker Compose.

---

## 📖 Index
1. Features
2. Prerequisites
3. Why Use a Reverse Proxy?
4. Problem Solved by Docker Compose vs Docker
5. Project Structure
6. Setup Instructions
    - Step 1: Clone the Repository
    - Step 2: Create the FastAPI Application
    - Step 3: Setup NGINX Reverse Proxy
    - Step 4: Create `docker-compose.yml`
    - Step 5: Build and Run the Containers
    - Step 6: Add Entry in `/etc/hosts`
7. Accessing Swagger UI
8. Testing the Setup
9. Stopping and Cleaning Up
10. Summary
11. Dockerfile Explanation
12. Docker Compose File Explanation
13. Author
14. Thank You

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

## 4️⃣ Problem Solved by Docker Compose vs Docker
Instead of running separate `docker run` commands, **Docker Compose** simplifies the process by managing multi-container applications with a single configuration file.

## 5️⃣ Project Structure
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

## 6️⃣ Setup Instructions

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

Create `Dockerfile` in `app/`:
```Dockerfile
FROM python:3.9  # Use official Python 3.9 base image
WORKDIR /app  # Set the working directory inside the container
COPY main.py .  # Copy the FastAPI application file to the container
RUN pip install fastapi uvicorn  # Install dependencies
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]  # Start FastAPI server
```

### Step 3: Setup NGINX Reverse Proxy
Inside `nginx/`, create `nginx.conf`:
```nginx
server {
    listen 80;
    location / {
        proxy_pass http://fastapi:8000/;
    }
}
```

### Step 4: Create `docker-compose.yml`
```yaml
version: '3.8'  # Define the Docker Compose file version
services:
  fastapi:
    build: ./app  # Build FastAPI image from Dockerfile inside app/
    container_name: fastapi_app  # Assign a custom container name
    ports:
      - "8000:8000"  # Map container port 8000 to host port 8000
  nginx:
    image: nginx:latest  # Use latest NGINX image from Docker Hub
    container_name: nginx_proxy  # Assign a custom container name
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf  # Mount custom NGINX config file
    ports:
      - "80:80"  # Map container port 80 to host port 80
    depends_on:
      - fastapi  # Ensure FastAPI starts before NGINX
```

### Step 5: Build and Run the Containers
```sh
docker-compose up --build -d
```

### Step 6: Add Entry in `/etc/hosts`
Edit `/etc/hosts` file and add:
```
127.0.0.1   fastapi.local
```

## 7️⃣ Accessing Swagger UI
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

## 8️⃣ Testing the Setup
Open your browser and visit:
```
http://fastapi.local
```
Or use `curl`:
```sh
curl http://fastapi.local
```

## 9️⃣ Stopping and Cleaning Up
```sh
docker-compose down
```

## 🔟 Summary
- FastAPI serves the API on port **8000**
- NGINX acts as a reverse proxy on port **80**
- Docker Compose manages both containers
- Swagger UI is accessible at `/docs`

## 🔟 Dockerfile Explanation
- `FROM python:3.9` → Uses the official Python 3.9 base image.
- `WORKDIR /app` → Sets the working directory inside the container.
- `COPY main.py .` → Copies the FastAPI application file into the container.
- `RUN pip install fastapi uvicorn` → Installs required dependencies.
- `CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]` → Runs the FastAPI application with Uvicorn.

## 🔟 Docker Compose File Explanation
- `version: '3.8'` → Defines the version of Docker Compose syntax.
- `services:` → Defines two services: **fastapi** and **nginx**.
- **FastAPI Service:**
  - `build: ./app` → Builds an image using the `Dockerfile` inside `app/`.
  - `container_name: fastapi_app` → Names the container.
  - `ports: "8000:8000"` → Maps FastAPI port 8000 to the host.
- **NGINX Service:**
  - `image: nginx:latest` → Uses the latest NGINX image from Docker Hub.
  - `container_name: nginx_proxy` → Names the NGINX container.
  - `volumes: ./nginx/nginx.conf:/etc/nginx/nginx.conf` → Mounts the custom NGINX config.
  - `ports: "80:80"` → Maps NGINX port 80 to the host.
  - `depends_on: fastapi` → Ensures FastAPI runs before NGINX starts.

## 🔟 Author
[T S Sundar Raj]

## 🙏 Thank You!
Happy coding! 🎉

