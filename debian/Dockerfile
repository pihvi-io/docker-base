FROM debian:jessie
MAINTAINER Onni Hakala <onni@keksi.io>

# Build arguments
ARG S6_OVERLAY_VERSION=v1.19.1.1
ARG S6_OVERLAY_SHA256=b5d360383dd519a33bd39651c43c49b4cf0e95344a94ba65dd8628eefd9ee5cb

# Finland is quite nice place to live.
# Instead of forking this you should move your living address here.
ENV TZ="Europe/Helsinki" \
    DEBIAN_FRONTEND=noninteractive

# Set debconf to run non-interactively
RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections

RUN set -x \

    # Add all deb-src addresses
    && echo "deb http://deb.debian.org/debian jessie main\n\
deb-src http://deb.debian.org/debian jessie main\n\
deb http://deb.debian.org/debian jessie-updates main\n\
deb-src http://deb.debian.org/debian jessie-updates main\n\
deb http://security.debian.org jessie/updates main\n\
deb-src http://security.debian.org/ jessie/updates main" > /etc/apt/sources.list \

    # Update sources and upgrade all packages
    && apt-get update -y \
    && apt-get upgrade -y \

    ##
    # Rebuild pam with --disable-audit flag
    # Fixes: https://github.com/docker/docker/issues/5899
    ##
    && apt-get -y build-dep -o APT::Get::Build-Dep-Automatic=true pam \
    && cd /tmp/ \
    && export CONFIGURE_OPTS=--disable-audit \
    && apt-get -b source pam \
    && dpkg -i libpam-doc*.deb libpam-modules*.deb libpam-runtime*.deb libpam0g*.deb \
    && rm -rf /tmp/* \
    && unset CONFIGURE_OPTS \

    # Install curl and ca-certificates
    && apt-get install -y --no-install-recommends curl ca-certificates \

    # This folder is in $PATH by default but isn't created by default
    && mkdir -p /usr/local/sbin \
    && cd /usr/local/sbin \

    # Install sha256sum validator to check that we download the right files
    && curl -L -o validate_sha256sum https://gist.githubusercontent.com/onnimonni/b49779ebc96216771a6be3de46449fa1/raw/d3ef37ab4a653e1b7655df55dfeadd54e0bacf84/validate_sha256sum \
    # This is semi meta but validate that our validator is valid
    && sha256sum validate_sha256sum | grep 0f7b790036f7cd00610cbe9e79c5b6b42d5b0e02beaff9549bdc43fc99910709 \
    && echo "Success: validate_sha256sum matches provided sha256sum" || exit 1 \
    && chmod +x validate_sha256sum \

    # Give execution rights to all scripts which we downloaded
    && chmod a+x * \

    ##
    # Create a few aliases
    # - I didn't figure out how to load aliases into sh shell with docker so we add scripts instead
    ##

    # ll
    && echo "#!/bin/sh \nls -lah \"\$@\"" > /usr/local/bin/ll \
    # la
    && echo "#!/bin/sh \nls -A \"\$@\"" > /usr/local/bin/la \
    # l
    && echo "#!/bin/sh \nls -CF \"\$@\"" > /usr/local/bin/l \

    && chmod a+x /usr/local/bin/* \

    # Cleanup
    && apt-get -y autoremove \
    && apt-get -y clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/log/apt/*

RUN \
    ##
    # Add S6-overlay to use S6 process manager
    # source: https://github.com/just-containers/s6-overlay/#the-docker-way
    ##
    && curl -L -o /tmp/s6-overlay-amd64.tar.gz https://github.com/just-containers/s6-overlay/releases/download/${S6_OVERLAY_VERSION}/s6-overlay-amd64.tar.gz \
    && validate_sha256sum /tmp/s6-overlay-amd64.tar.gz ${S6_OVERLAY_SHA256} \
    && tar -zxvf /tmp/s6-overlay-amd64.tar.gz -C \
    && rm -rf /tmp/*

COPY rootfs/ /

ENTRYPOINT ["/init"]
