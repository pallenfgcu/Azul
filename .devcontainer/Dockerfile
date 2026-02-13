# .devcontainer/Dockerfile
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    build-essential \
    git \
    ca-certificates \
    ninja-build \
    cmake \
    pkg-config \
    gdb \
    # SFML build deps (X11/OpenGL/audio/codecs)
    libx11-dev \
    libxrandr-dev \
    libxcursor-dev \
    libxinerama-dev \
    libxi-dev \
    libudev-dev \
    libgl1-mesa-dev \
    libopenal-dev \
    libvorbis-dev \
    libflac-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspaces
