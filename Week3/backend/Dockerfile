# =========================
# Stage 1: Builder
# =========================
FROM python:3.11-slim AS builder

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /build

# Build dependencies only needed during installation
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       gcc \
       default-libmysqlclient-dev \
       pkg-config \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .

# Install Python dependencies into a separate directory
RUN pip install --no-cache-dir --prefix=/install -r requirements.txt


# =========================
# Stage 2: Runtime
# =========================
FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Runtime library required by MySQL client
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       default-libmysqlclient-dev \
       curl \
    && rm -rf /var/lib/apt/lists/*

# Copy only installed Python packages from builder
COPY --from=builder /install /usr/local

# Copy application
COPY . .

# Run as non-root user
RUN useradd -m appuser \
    && chown -R appuser:appuser /app

USER appuser

EXPOSE 5000

# Health check
HEALTHCHECK --interval=15s --timeout=5s --start-period=10s --retries=5 \
    CMD curl -f http://127.0.0.1:5000/api/health || exit 1

CMD ["gunicorn", "--bind", "0.0.0.0:5000", "--workers", "3", "--timeout", "60", "app:app"]
