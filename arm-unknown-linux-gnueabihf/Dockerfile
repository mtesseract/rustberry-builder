FROM ubuntu:20.04

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get dist-upgrade --yes \
    && apt-get install --yes make binutils curl zip pkg-config libssl-dev gcc-multilib \
    lib32z1 git wget
# WHY lib32z1? -MC

ARG RASPBERRY_PI_TOOLS_COMMIT_ID=5caa7046982f0539cf5380f94da04b31129ed521
RUN curl -sL https://github.com/raspberrypi/tools/archive/5caa7046982f0539cf5380f94da04b31129ed521.tar.gz \
    | tar xzf - -C /usr/local --strip-components=1 tools-5caa7046982f0539cf5380f94da04b31129ed521/arm-bcm2708

# This one does not seem to work due to 'ld' ending up in path for ARM.
# ENV PATH=/usr/local/arm-bcm2708/arm-linux-gnueabihf/bin:/usr/local/arm-bcm2708/arm-bcm2708-linux-gnueabi/arm-bcm2708-linux-gnueabi/bin:$PATH
ENV PATH=/usr/local/arm-bcm2708/arm-linux-gnueabihf/bin:$PATH
ENV PATH=/usr/local/arm-bcm2708/arm-linux-gnueabihf/libexec/gcc/arm-linux-gnueabihf/4.9.3:$PATH
ENV CC=arm-linux-gnueabihf-gcc
ENV CC_x86_64_unknown_linux_gnu=gcc
ENV CC_arm_unknown_linux_gnu=arm-linux-gnueabihf-gcc

# Setup MUSL
# need g++, wget

# RUN cd /usr/local \
#     && git clone https://github.com/richfelker/musl-cross-make \
#     && cd musl-cross-make


# MUSL configuration:
#
# TARGET = arm-linux-musleabihf
# MUSL_CONFIG += --disable-shared
# OUTPUT =/opt/cross/armv6/

# CC_arm_unknown_linux_gnu=arm-linux-gnueabihf-gcc

# CAVEAT: backtrace-sys requires CC=gcc. Other crates require the arm cross compiler in $CC.

# for zlib cross
# CHOST=arm AR=/usr/local/arm-bcm2708/arm-linux-gnueabihf/bin/arm-linux-gnueabihf-ar RANLIB=/usr/local/arm-bcm2708/arm-linux-gnueabihf/bin/arm-linux-gnueabihf-ranlib ./configure --prefix=/usr/local

# Install zlib, is this necessary?

# Build zlib for ARM
RUN cd /usr/local && \
    VERS=1.2.11 && \
    curl -O https://www.zlib.net/zlib-$VERS.tar.gz && \
    tar xvf zlib-$VERS.tar.gz && \
    cd zlib-$VERS && \
    CHOST=arm \
    AR=$(which arm-linux-gnueabihf-ar) \
    RANLIB=$(which arm-linux-gnueabihf-ranlib) \
    ./configure --prefix=/usr/local && \
    make && \
    make install && \
    echo disabled: rm -r zlib-$VERS.tar.gz zlib-$VERS

# Build OpenSSL for ARM
RUN cd /usr/local && \
    VERS="1.1.1g" && \
    curl -O "https://www.openssl.org/source/openssl-$VERS.tar.gz" && \
    tar xzf "openssl-$VERS.tar.gz" && \
    echo disabled: mv openssl-$VERS openssl && \
    export MACHINE=armv6 && \
    export ARCH=arm && \
    echo disabled: export CC=arm-linux-gnueabihf-gcc && \
    cd openssl-$VERS && \
    ./Configure linux-generic32 shared --prefix=/usr/local --openssldir=/usr/local && \
    make && \
    make install && \
    echo disabled: rm -r openssl-$VERS.tar.gz openssl

# Build ALSA for ARM
RUN cd /usr/local && \
    curl -O https://www.alsa-project.org/files/pub/lib/alsa-lib-1.2.2.tar.bz2 && \
    tar xjf alsa-lib-1.2.2.tar.bz2 && \
    cd alsa-lib-1.2.2 && \
    ./configure --host=arm-linux-gnueabihf && \
    make && \
    make install && \
    echo disabled: rm -r alsa-lib-1.2.2.tar.bz2 alsa-lib-1.2.2


RUN curl --proto "=https" --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y \
    && . ~/.cargo/env \
    && rustup target add arm-unknown-linux-gnueabihf \
    && echo '[target.arm-unknown-linux-gnueabihf]\nlinker = "arm-linux-gnueabihf-gcc"' > ~/.cargo/config

ENV PKG_CONFIG_ALLOW_CROSS=1
ENV PATH="/root/.cargo/bin:$PATH"
ENV CARGO_HOME=/root/.cargo
ENV RUSTUP_HOME=/root/.rustup

# ENV OPENSSL_LIB_DIR=/usr/local
# ENV OPENSSL_INCLUDE_DIR=/usr/local/include
# ENV OPENSSL_LIB_DIR=/usr/local/openssl
# ENV OPENSSL_INCLUDE_DIR=/usr/local/openssl/include

