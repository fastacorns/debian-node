#########################
# Base image
#########################
FROM node:bookworm-slim AS base
WORKDIR /site

# Install latest security upgrades, and crucial runtimes.
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y \
        curl \
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
        awscli \
        build-essential \
        ca-certificates \
        gettext \
        git \
        gnupg \
        jq \
        python3 \
        sudo \
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
