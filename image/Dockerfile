# Copyright Broadcom, Inc. All Rights Reserved.
# SPDX-License-Identifier: APACHE-2.0

ARG DEBIAN_SNAPSHOT="trixie"

FROM debian:${DEBIAN_SNAPSHOT}-slim

# get latest version: apt policy slapd
ARG VERSION_OPENLDAP="2.6.10+dfsg-1"

SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]
# Install required system packages and dependencies
RUN apt-get update && \
    apt-get upgrade -y && \
    LC_ALL=C DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    libcap2-bin \
    patch \
    slapd=${VERSION_OPENLDAP} \
    ldap-utils=${VERSION_OPENLDAP} && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/archives

RUN rm -rf "/etc/ldap/slapd.d/" "/var/lib/ldap/" && \
    mkdir -p /run/slapd/ && chmod g+rwX /run/slapd/

COPY prebuildfs /
RUN find / -perm /6000 -type f -exec chmod a-s {} \; || true

COPY rootfs /

RUN patch -p0 -d /opt/bitnami/scripts/ < /opt/bitnami/scripts/custom.patch && \
    rm /opt/bitnami/scripts/custom.patch && \
    chmod +x /opt/bitnami/scripts/openldap/*.sh && \
    /opt/bitnami/scripts/openldap/postunpack.sh

ENV BITNAMI_APP_NAME="openldap"

EXPOSE 1389 1636

USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/openldap/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/openldap/run.sh" ]
