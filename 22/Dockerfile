#########################
# Base image
# Using Trixie (Debian 13 testing) for Ghostscript 10.05.1+
# Bookworm's GS 10.0.0 has a bug that crashes on PDF transparency flattening
#########################
FROM node:22-trixie-slim AS base
WORKDIR /site

# Install latest security upgrades, and crucial runtimes.
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y \
        curl \
        ghostscript \
        poppler-utils \
        qpdf \
    && rm -rf /var/lib/apt/lists/* \
    && npm --global install npm pm2 npx \
    && rm -rf /opt/yarn* \
    && rm -rf /root/.npm \
    && rm -rf /tmp/*

#########################
# Builder image
#########################
FROM base AS build
WORKDIR /site

# Install latest security upgrades, and crucial runtimes.
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y \
        build-essential \
        ca-certificates \
        gettext \
        git \
        gnupg \
        jq \
        python3 \
        sudo \
        unzip \
    && curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "/tmp/awscliv2.zip" \
    && unzip /tmp/awscliv2.zip -d /tmp \
    && /tmp/aws/install \
    && rm -rf /tmp/aws* \
    && cp /usr/bin/envsubst /usr/local/bin/envsubst \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /opt/yarn* \
    && rm -rf /root/.npm \
    && rm -rf /tmp/*

# Install Docker
RUN sudo install -m 0755 -d /etc/apt/keyrings \
    && curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg \
    && sudo chmod a+r /etc/apt/keyrings/docker.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
        $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null \
    && apt-get update \
    && apt-get install -y \
        docker-ce \
        docker-ce-cli \
        containerd.io \
        docker-buildx-plugin \
        docker-compose-plugin \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /opt/yarn* \
    && rm -rf /root/.npm \
    && rm -rf /tmp/*
