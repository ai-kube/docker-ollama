FROM debian:stable-slim

ARG WORKER_UID=1000
ARG WORKER_GID=1000
ARG WORKER_HOME=/app
ARG WORKER=ollama
ARG OLLAMA_REL_URL=https://github.com/ollama/ollama/releases/latest/download/ollama-linux-amd64.tgz

USER root

# Set up curl and certificates
RUN --mount=type=tmpfs,target=/var/cache/apt \
    --mount=type=tmpfs,target=/var/lib/apt \
set -ex \
&&  apt-get update \
&&  apt-get install -y --no-install-recommends curl \
&&  apt-get clean \
&&  rm -rf /var/lib/apt/lists/*

# Set up the default user 'ollama'
RUN set -ex \
&&  groupadd -g ${WORKER_GID} ${WORKER} \
&&  useradd -u ${WORKER_UID} -g ${WORKER_GID} -c "Ollama User" -m --shell /bin/bash -d ${WORKER_HOME} ${WORKER} \
&&  mkdir -p ${WORKER_HOME}/.ollama \
&&  chown -R ${WORKER}:${WORKER} ${WORKER_HOME}

# Grab ollama released binary for the platform
RUN set -ex \
&&  mkdir -p /opt/ollama \
&&  curl -L ${OLLAMA_REL_URL} -o - | tar -C /opt/ollama -xzf - \
&&  chown -R 0:0 /opt/ollama

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
