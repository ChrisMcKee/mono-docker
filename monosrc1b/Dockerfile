FROM debian:buster-slim

# Install tools
RUN \
    DEBIAN_FRONTEND=noninteractive apt update && \
    DEBIAN_FRONTEND=noninteractive apt upgrade -y && \
    DEBIAN_FRONTEND=noninteractive apt install -y git autoconf libtool automake build-essential gettext cmake python3

# Set installation prefix
ENV PREFIX=/usr/local

# Ensure PREFIX exists with proper permissions
RUN mkdir -p $PREFIX && \
    chown -R root:root $PREFIX

# Add PREFIX/bin to PATH
ENV PATH=$PREFIX/bin:$PATH

# Clone and build Mono
RUN git clone --depth 1 https://gitlab.winehq.org/mono/mono.git && \
    cd mono && \
    ./autogen.sh --prefix=$PREFIX && \
    make && \
    make install

# We're done
WORKDIR /