FROM --platform=linux/amd64 debian:stable-slim

ARG WORKER_UID=1000
ARG WORKER_GID=1000
ARG WORKER_HOME=/app
ARG WORKER=ollama

USER root

# Set up curl and certificates
RUN set -ex \
&&  apt-get update \
&&  apt-get install -y curl \
&&  rm -rf /var/lib/apt/lists/*

# Set up the default user 'ollama'
RUN set -ex \
&&  groupadd -g ${WORKER_GID} ${WORKER} \
&&  useradd -u ${WORKER_UID} -g ${WORKER_GID} -c "Ollama User" -m --shell /bin/bash -d ${WORKER_HOME} ${WORKER} \
&&  mkdir -p ${WORKER_HOME}/.ollama \
&&  chown -R ${WORKER}:${WORKER} ${WORKER_HOME}

# Grab ollama released binary for the platform
RUN set -ex \
&&  curl -L https://github.com/ollama/ollama/releases/latest/download/ollama-linux-amd64.tgz -o /tmp/ollama.tgz \
&&  mkdir -p /opt/ollama \
&&  tar -C /opt/ollama -xzf /tmp/ollama.tgz \
&&  chown -R 0:0 /opt/ollama \
&&  rm -f /tmp/ollama.tgz

USER ${WORKER}

ENV HOME=${WORKER_HOME}
ENV OLLAMA_HOST=0.0.0.0
ENV PATH=/opt/ollama/bin:${PATH}
ENV LD_LIBRARY_PATH=/usr/local/nvidia/lib:/usr/local/nvidia/lib64:/opt/ollama/lib
ENV NVIDIA_DRIVER_CAPABILITIES=compute,utility
ENV NVIDIA_VISIBLE_DEVICES=all

WORKDIR ${HOME}
VOLUME ${HOME}/.ollama

ENTRYPOINT [ "ollama" ]

CMD [ "serve" ]
