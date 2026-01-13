#########################
# Ghostscript build stage
# Building Ghostscript 10.06.0 from source (Trixie's 10.05 has SIGSEGV bugs)
#########################
FROM node:22-trixie-slim AS gs-builder

RUN apt-get update \
    && apt-get install -y \
        build-essential \
        curl \
        libpng-dev \
        libjpeg-dev \
        libtiff-dev \
        libfreetype6-dev \
        libfontconfig1-dev \
        liblcms2-dev \
        libopenjp2-7-dev \
        zlib1g-dev \
    && curl -fsSL "https://github.com/ArtifexSoftware/ghostpdl-downloads/releases/download/gs10060/ghostscript-10.06.0.tar.gz" -o /tmp/gs.tar.gz \
    && tar -xzf /tmp/gs.tar.gz -C /tmp \
    && cd /tmp/ghostscript-10.06.0 \
    && ./configure --prefix=/usr/local --disable-cups --without-tesseract \
    && make -j$(nproc) \
    && make install

#########################
# Base image
# Using Trixie (Debian 13 testing) for recent poppler-utils and qpdf
#########################
FROM node:22-trixie-slim AS base
WORKDIR /site

# Install latest security upgrades, crucial runtimes, and GS runtime dependencies
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y \
        curl \
        poppler-utils \
        qpdf \
        img2pdf \
        # Runtime libraries for Ghostscript
        libpng16-16t64 \
        libjpeg62-turbo \
        libtiff6 \
        libfreetype6 \
        libfontconfig1 \
        liblcms2-2 \
        libopenjp2-7 \
    && rm -rf /var/lib/apt/lists/* \
    && npm --global install npm pm2 npx \
    && rm -rf /opt/yarn* \
    && rm -rf /root/.npm \
    && rm -rf /tmp/*

# Copy Ghostscript 10.06 from builder
COPY --from=gs-builder /usr/local/bin/gs /usr/local/bin/gs
COPY --from=gs-builder /usr/local/share/ghostscript /usr/local/share/ghostscript

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
