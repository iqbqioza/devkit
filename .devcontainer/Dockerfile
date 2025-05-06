FROM debian:bookworm-slim

# Install dependencies
RUN apt-get update && apt-get install -y \
    curl \
    apt-transport-https \
    gnupg \
    ca-certificates \
    git \
    && rm -rf /var/lib/apt/lists/*

# Install Doppler CLI
RUN curl -sLf --retry 3 --tlsv1.2 --proto "=https" 'https://packages.doppler.com/public/cli/gpg.DE2A7741A397C129.key' | gpg --dearmor -o /usr/share/keyrings/doppler-archive-keyring.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/doppler-archive-keyring.gpg] https://packages.doppler.com/public/cli/deb/debian any-version main" | tee /etc/apt/sources.list.d/doppler.list \
    && apt-get update && apt-get install -y doppler \
    && rm -rf /var/lib/apt/lists/*

# Set up NVM and Node.js
ENV NVM_VERSION=v0.40.3
ENV NODE_VERSION=v22.15.0

# Create non-root user
RUN groupadd -g 1000 vscode \
    && useradd -u 1000 -g vscode -ms /bin/bash vscode

# Switch to non-root user
USER vscode

# Set up NVM for the non-root user
ENV NVM_DIR=/home/vscode/.nvm
RUN mkdir -p $NVM_DIR \
    && curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/$NVM_VERSION/install.sh | bash \
    && . $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
    && nvm use default \
    && npm i -g pnpm

# Security updates
USER root
RUN apt-get update && apt-get upgrade -y \
    && rm -rf /var/lib/apt/lists/*

USER vscode
EXPOSE 3000