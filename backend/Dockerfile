# Stage 1: Builder
FROM python:3.12-slim AS builder

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    gcc \
    libpq-dev \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Install Python dependencies
COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

# -------------------
# Stage 2: Runtime
FROM python:3.12-slim

WORKDIR /app

# Create non-root user
RUN groupadd -g 1001 appgroup && \
    useradd -r -u 1001 -g appgroup appuser

# Copy Python dependencies
COPY --from=builder /root/.local /home/appuser/.local

# Copy application code
COPY . .

# Set permissions
RUN chown -R appuser:appgroup /app

USER appuser

# Environment variables
ENV PATH="/home/appuser/.local/bin:$PATH"
ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1

# Create static directory
RUN mkdir -p static

# Expose port
EXPOSE 8000

# Run Django (with debug false by default)
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]