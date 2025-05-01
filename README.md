# flask-app-docker-testing

### Dockerfile
```properties
# Base image (OS)
FROM python:3.9-slim

# Working directory
WORKDIR /app

# Copy src code to container
COPY . .

# Run the build commands
RUN pip install -r requirements.txt

# expose port 80
EXPOSE 80

# serve the app / run the app (keep it running)
CMD ["python","run.py"]
```

### Dockerfile-multi
```properties
# Build Stage
FROM python:3.9-slim AS builder
WORKDIR /app

# Copy dependency file first for efficient caching
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt --target=/app/deps

# Copy application source code
COPY . .

# Final Distroless Stage
FROM gcr.io/distroless/python3-debian11
WORKDIR /app

# Copy dependencies from the builder stage
COPY --from=builder /app/deps /app/deps
COPY --from=builder /app .

# Set environment variables
ENV PYTHONUNBUFFERED=1
ENV PYTHONPATH="/app/deps"

# Expose the application port
EXPOSE 80

# Run the application
CMD ["run.py"]

```
