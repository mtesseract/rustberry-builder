FROM ubuntu:latest

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get dist-upgrade --yes \
    && apt-get install --yes binutils make curl git gcc gcc-multilib-arm-linux-gnueabihf pkg-config libssl-dev openssh-server

RUN cd /usr/local && \
    VERS="1.1.1d" && \
    curl -O "https://www.openssl.org/source/openssl-$VERS.tar.gz" && \
    tar xzf "openssl-$VERS.tar.gz" && \
    mv openssl-$VERS openssl && \
    export MACHINE=armv7 && \
    export ARCH=arm && \
    export CC=arm-linux-gnueabihf-gcc && \
    cd openssl && \
    ./config shared && \
    make && \
    cd /usr/local && \
    curl -O https://www.alsa-project.org/files/pub/lib/alsa-lib-1.2.2.tar.bz2 && \
    tar xjf alsa-lib-1.2.2.tar.bz2 && \
    cd alsa-lib-1.2.2 && \
    export CC=arm-linux-gnueabihf-gcc && \
    ./configure --host=arm-linux-gnueabihf && \
    make && \
    make install

RUN curl --proto "=https" --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y \
    && . ~/.cargo/env \
    && rustup target add armv7-unknown-linux-gnueabihf \
    && cargo install cargo-build-deps \
    && echo "[target.armv7-unknown-linux-gnueabihf]\nlinker = \"arm-linux-gnueabihf-gcc\"\n" > ~/.cargo/config \
    && cargo install cargo-watch
