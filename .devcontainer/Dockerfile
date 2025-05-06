FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    curl \
    apt-transport-https \
    gnupg \
    ca-certificates \
    unzip \
    git \
    apt-utils \
    && rm -rf /var/lib/apt/lists/*

RUN curl -sLf --retry 3 --tlsv1.2 --proto "=https" 'https://packages.doppler.com/public/cli/gpg.DE2A7741A397C129.key' | gpg --dearmor -o /usr/share/keyrings/doppler-archive-keyring.gpg

RUN echo "deb [signed-by=/usr/share/keyrings/doppler-archive-keyring.gpg] https://packages.doppler.com/public/cli/deb/debian any-version main" | tee /etc/apt/sources.list.d/doppler.list

RUN apt-get update && apt-get install -y doppler

ENV NVM_VERSION=v0.40.3
ENV NVM_DIR=/usr/local/nvm
ENV NODE_VERSION=v22.15.0

# Create group and user
RUN groupadd -g 1000 vscode \
    && useradd -u 1000 -g vscode -ms /bin/bash vscode \
    && mkdir -p /usr/local/nvm \
    && chown -R vscode:vscode /usr/local/nvm

# Switch to non-root user
USER vscode

# Set up NVM for the non-root user
ENV NVM_DIR=/home/vscode/.nvm
RUN mkdir -p $NVM_DIR \
    && curl https://raw.githubusercontent.com/nvm-sh/nvm/$NVM_VERSION/install.sh | bash \
    && . $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
    && nvm use default \
    && npm i -g pnpm

EXPOSE 3000