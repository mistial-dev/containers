# Copyright Broadcom, Inc. All Rights Reserved.
# SPDX-License-Identifier: APACHE-2.0

FROM docker.io/bitnami/minideb:bookworm as builder

ARG TARGETARCH

ENV OS_ARCH="${TARGETARCH:-amd64}"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]

# Install required system packages and dependencies
RUN install_packages ca-certificates curl
RUN mkdir -p /tmp/bitnami/pkg/cache/ ; cd /tmp/bitnami/pkg/cache/ ; \
    COMPONENTS=( \
      "kubeapps-apprepository-controller-2.11.0-3-linux-${OS_ARCH}-debian-12" \
    ) ; \
    for COMPONENT in "${COMPONENTS[@]}"; do \
      if [ ! -f "${COMPONENT}.tar.gz" ]; then \
        curl -SsLf "https://downloads.bitnami.com/files/stacksmith/${COMPONENT}.tar.gz" -O ; \
        curl -SsLf "https://downloads.bitnami.com/files/stacksmith/${COMPONENT}.tar.gz.sha256" -O ; \
      fi ; \
      sha256sum -c "${COMPONENT}.tar.gz.sha256" ; \
      tar -zxf "${COMPONENT}.tar.gz" -C /opt/bitnami --strip-components=2 --no-same-owner --wildcards '*/files' ; \
      rm -rf "${COMPONENT}".tar.gz{,.sha256} ; \
    done

######

FROM scratch

ARG TARGETARCH

ENV OS_ARCH="${TARGETARCH:-amd64}"

LABEL com.vmware.cp.artifact.flavor="sha256:c50c90cfd9d12b445b011e6ad529f1ad3daea45c26d20b00732fae3cd71f6a83" \
      org.opencontainers.image.base.name="scratch" \
      org.opencontainers.image.created="2024-10-02T02:38:15Z" \
      org.opencontainers.image.description="Application packaged by Broadcom, Inc." \
      org.opencontainers.image.licenses="Apache-2.0" \
      org.opencontainers.image.ref.name="2.11.0-debian-12-r5" \
      org.opencontainers.image.title="kubeapps-apprepository-controller" \
      org.opencontainers.image.vendor="Broadcom, Inc." \
      org.opencontainers.image.version="2.11.0"

COPY prebuildfs /
COPY rootfs /
COPY --from=builder /opt/bitnami/kubeapps-apprepository-controller/bin/apprepository-controller /apprepository-controller
COPY --from=builder /opt/bitnami/kubeapps-apprepository-controller/.spdx-kubeapps-apprepository-controller.spdx /opt/bitnami/kubeapps-apprepository-controller/.spdx-kubeapps-apprepository-controller.spdx
COPY --from=builder /opt/bitnami/kubeapps-apprepository-controller/licenses /opt/bitnami/kubeapps-apprepository-controller/licenses

ENV APP_VERSION="2.11.0" \
    BITNAMI_APP_NAME="kubeapps-apprepository-controller"

USER 1001

CMD [ "/apprepository-controller" ]
