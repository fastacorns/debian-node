#########################
# Base image
#########################
FROM node:bookworm-slim AS base
WORKDIR /site

# Install latest security upgrades, and crucial runtimes.
RUN apt-get update \
    && apt-get install -y \
        curl \
    && rm -rf /var/lib/apt/lists/* \
    && npm --global install npm pm2 \
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
    && apt-get install -y \
        build-essential \
        python3 \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /opt/yarn* \
    && rm -rf /root/.npm \
    && rm -rf /tmp/*
