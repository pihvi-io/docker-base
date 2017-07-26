FROM alpine:3.6
MAINTAINER Onni Hakala <onni@keksi.io>

RUN set -x \
    
    # Install wget curl and ca-certificates
    && apk --update add wget curl ca-certificates \

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

    && chmod a+x /usr/local/bin/* 

##
# Install gomplate for fast envsubst like templates but with more logic
##
ARG GOTEMPLATE_VERSION=v1.9.1
ADD https://github.com/hairyhenderson/gomplate/releases/download/${GOTEMPLATE_VERSION}/gomplate_linux-amd64-slim /usr/local/bin/gomplate

##
# Install forego for running multiple processes
##
RUN wget https://bin.equinox.io/c/ekMN3bCZFUn/forego-stable-linux-amd64.tgz -O - | tar xz -C /usr/local/bin/ \
    && chmod +x /usr/local/bin/*