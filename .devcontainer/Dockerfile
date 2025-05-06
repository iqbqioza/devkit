FROM debian:bookworm-slim

# Set user variables
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=1000

ENV USERNAME=${USERNAME}
ENV USER_UID=${USER_UID}
ENV USER_GID=${USER_GID}

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
RUN groupadd -g ${USER_GID} ${USERNAME} \
    && useradd -u ${USER_UID} -g ${USERNAME} -ms /bin/bash ${USERNAME}

# Switch to non-root user
USER ${USERNAME}

# Set up NVM for the non-root user
ENV NVM_DIR=/home/${USERNAME}/.nvm
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

USER ${USERNAME}
EXPOSE 3000