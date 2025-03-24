# Use a smaller base image (python:3.9-slim)
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy only the requirements file first (this allows Docker to cache layer for dependencies)
COPY requirements.txt .

# Install dependencies from the requirements.txt (use --no-cache-dir to avoid caching)
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the FastAPI app files
COPY . .

# Expose FastAPI port
EXPOSE 8000

# Run FastAPI server
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
