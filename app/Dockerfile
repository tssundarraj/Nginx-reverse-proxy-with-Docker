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
