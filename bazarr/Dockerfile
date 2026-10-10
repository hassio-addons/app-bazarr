ARG BUILD_FROM=ghcr.io/hassio-addons/base:21.0.3
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base
ARG BAZARR_VERSION=1.6.0
ARG UNRAR_VERSION=6.2.12
# hadolint ignore=DL3003,DL3042
RUN \
  apk add --no-cache --virtual .build-dependencies \
    autoconf=2.73-r0 \
    automake=1.18.1-r1 \
    build-base=0.5-r4 \
    linux-headers=7.0.0-r1 \
    musl-dev=1.2.6-r2 \
    py3-pip=26.1.2-r0 \
    python3-dev=3.14.7-r1 \
  \
  && apk add --no-cache \
    7zip=26.01-r0 \
    ffmpeg=8.1.2-r0 \
    mediainfo=26.01-r0 \
    py3-setuptools=82.0.1-r1 \
    python3=3.14.8-r0 \
    unzip=6.0-r16 \
  \
  && curl -J -L -o /tmp/unrar.tar.gz \
      "https://www.rarlab.com/rar/unrarsrc-${UNRAR_VERSION}.tar.gz" \
  && tar zxvf \
      /tmp/unrar.tar.gz \
      -C /tmp \
  \
  && cd /tmp/unrar \
  && make \
  && install -v -m755 unrar /usr/local/bin \
  && cd - \
  \
  && curl -J -L -o /tmp/bazarr.zip \
      "https://github.com/morpheus65535/bazarr/releases/download/v${BAZARR_VERSION}/bazarr.zip" \
  && unzip \
      /tmp/bazarr.zip \
      -d /opt/bin \
  \
  && echo -e "PackageVersion=${BAZARR_VERSION}\nUpdateMethod=Docker\nBranch=master\nPackageAuthor=Home Assistant Community Apps" > "/opt/package_info" \
  \
  && cd /opt/bin \
  && pip3 install -r requirements.txt \
  \
  && apk del --no-cache --purge .build-dependencies \
  && rm -f -r \
    /tmp/*

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Franck Nijhof <opensource@frenck.dev>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Apps" \
    org.opencontainers.image.authors="Franck Nijhof <opensource@frenck.dev>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://frenck.dev/home-assistant-apps" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
