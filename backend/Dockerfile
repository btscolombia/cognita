FROM --platform=linux/amd64 python:3.11

# Required for opencv
RUN apt-get update -y && \
    apt-get install --no-install-recommends ffmpeg libsm6 libxext6 -y

# Set environment variables
ENV PATH=/virtualenvs/venv/bin:$PATH
RUN python3 -m venv /virtualenvs/venv/

# Copy requirements.txt
COPY backend/requirements.txt /tmp/requirements.txt
COPY backend/vectordb.requirements.txt /tmp/vectordb.requirements.txt

# --- FIX: Instalación manual de dependencias para Debian 12 (Bookworm) ---
RUN apt-get update && apt-get install -y --no-install-recommends \
    libasound2 \
    libatk-bridge2.0-0 \
    libgtk-3-0 \
    libnss3 \
    libx11-xcb1 \
    libxss1 \
    libxtst6 \
    libgbm1 \
    libpangocairo-1.0-0 \
    libxshmfence1 \
    git \
    wget \
    && rm -rf /var/lib/apt/lists/*

# --- Instalación de paquetes Python y Playwright ---
# Nota: Quitamos "--with-deps" porque ya las instalamos arriba manualmente
RUN python3 -m pip install -U pip setuptools wheel uv && \
    python3 -m uv pip install --no-cache-dir -r /tmp/requirements.txt --index-strategy unsafe-any-match && \
    playwright install chromium

# Install VectorDB packages
ARG ADD_VECTORDB=0
RUN if [ "${ADD_VECTORDB}" = "1" ]; then python3 -m uv pip install --no-cache-dir -r /tmp/vectordb.requirements.txt --index-strategy unsafe-any-match; fi

# Copy the project files
COPY . /app

# Set the working directory
WORKDIR /app
