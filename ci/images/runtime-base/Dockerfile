FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    git \
    jq \
    tar \
    cpio \
    busybox-static \
    python3-pip \
    golang-go \
    docker.io \
    qemu-system-x86 \
    qemu-utils \
    ovmf \
    iptables \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

RUN set -eux; \
    ARCH="$(uname -m)"; \
    case "$ARCH" in \
      x86_64) K_ARCH="amd64"; TALOS_ARCH="amd64" ;; \
      aarch64|arm64) K_ARCH="arm64"; TALOS_ARCH="arm64" ;; \
      *) echo "Unsupported arch: $ARCH" >&2; exit 1 ;; \
    esac; \
    K_VER="$(curl -fsSL https://dl.k8s.io/release/stable.txt)"; \
    curl -fsSL "https://dl.k8s.io/release/${K_VER}/bin/linux/${K_ARCH}/kubectl" -o /usr/local/bin/kubectl; \
    chmod +x /usr/local/bin/kubectl; \
    curl -fsSL "https://github.com/siderolabs/talos/releases/latest/download/talosctl-linux-${TALOS_ARCH}" -o /usr/local/bin/talosctl; \
    chmod +x /usr/local/bin/talosctl

RUN set -eux; \
    ARCH="$(uname -m)"; \
    case "$ARCH" in \
      x86_64) BX_ARCH="amd64" ;; \
      aarch64|arm64) BX_ARCH="arm64" ;; \
      *) echo "Unsupported arch: $ARCH" >&2; exit 1 ;; \
    esac; \
    BX_VERSION="$(curl -fsSL https://api.github.com/repos/docker/buildx/releases/latest | sed -n 's/.*"tag_name"[[:space:]]*:[[:space:]]*"\([^"]*\)".*/\1/p' | head -n1)"; \
    mkdir -p /root/.docker/cli-plugins; \
    curl -fsSL "https://github.com/docker/buildx/releases/download/${BX_VERSION}/buildx-${BX_VERSION}.linux-${BX_ARCH}" -o /root/.docker/cli-plugins/docker-buildx; \
    chmod +x /root/.docker/cli-plugins/docker-buildx
