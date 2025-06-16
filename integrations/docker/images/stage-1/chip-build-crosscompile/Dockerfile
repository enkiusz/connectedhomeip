ARG VERSION=1
ARG BASE_IMAGE_URL=ghcr.io/project-chip
ARG SOURCE_URL=https://github.com/project-chip/connectedhomeip

FROM ${BASE_IMAGE_URL}/chip-build:${VERSION} as build    # dependency
LABEL org.opencontainers.image.source ${SOURCE_URL}

RUN set -x \
  && apt-get update \
  && DEBIAN_FRONTEND=noninteractive apt-get install -fy --no-install-recommends \
  git \
  xz-utils \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/ \
  && : # last line

WORKDIR /opt
# Unpack the sysroot, while also removing some rather large items in it that
# are generally not required for compilation
RUN set -x \
  && git clone --depth 1 https://chromium.googlesource.com/chromium/tools/depot_tools.git /opt/depot_tools \
  # TODO: Remove experimental solution to create the sysroot file in cross-compile image
  && echo 'experimental/matter/sysroot/ubuntu-24.04-aarch64 version:build-2025.06.05' > ensure_file.txt \
  && ./depot_tools/cipd ensure -ensure-file ensure_file.txt -root ./ \
  && rm -rf /opt/ubuntu-24.04-aarch64-sysroot/usr/lib/firmware \
  && rm -rf /opt/ubuntu-24.04-aarch64-sysroot/usr/lib/git-core \
  && rm -rf /opt/ubuntu-24.04-aarch64-sysroot/usr/lib/modules \
  && rm -rf /opt/ubuntu-24.04-aarch64-sysroot/lib/firmware \
  && rm -rf /opt/ubuntu-24.04-aarch64-sysroot/lib/git-core \
  && rm -rf /opt/ubuntu-24.04-aarch64-sysroot/lib/modules \
  && : # last line

FROM ${BASE_IMAGE_URL}/chip-build:${VERSION}
LABEL org.opencontainers.image.source ${SOURCE_URL}

COPY --from=build /opt/ubuntu-24.04-aarch64-sysroot/ /opt/ubuntu-24.04-aarch64-sysroot/

ENV SYSROOT_AARCH64=/opt/ubuntu-24.04-aarch64-sysroot
