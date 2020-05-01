FROM ubuntu:latest

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get dist-upgrade --yes \
    && apt-get install --yes make binutils curl git pkg-config libssl-dev

ARG RASPBERRY_PI_TOOLS_COMMIT_ID=b0c869bc929587a7e1d20a98e2dc828a24ca396a
RUN curl -sL https://github.com/raspberrypi/tools/archive/$RASPBERRY_PI_TOOLS_COMMIT_ID.tar.gz \
    | tar xzf - -C /usr/local --strip-components=1 tools-${RASPBERRY_PI_TOOLS_COMMIT_ID}/arm-bcm2708

ENV PATH=/usr/local/arm-bcm2708/arm-linux-gnueabihf/bin:$PATH
ENV PATH=/usr/local/arm-bcm2708/arm-linux-gnueabihf/libexec/gcc/arm-linux-gnueabihf/4.9.3:$PATH
ENV CC=arm-linux-gnueabihf-gcc

RUN cd /usr/local && \
    VERS="1.1.1g" && \
    curl -O "https://www.openssl.org/source/openssl-$VERS.tar.gz" && \
    tar xzf "openssl-$VERS.tar.gz" && \
    mv openssl-$VERS openssl && \
    export MACHINE=armv6 && \
    export ARCH=arm && \
    cd openssl && \
    ./config shared && \
    make

RUN cd /usr/local && \
    curl -O https://www.alsa-project.org/files/pub/lib/alsa-lib-1.2.2.tar.bz2 && \
    tar xjf alsa-lib-1.2.2.tar.bz2 && \
    cd alsa-lib-1.2.2 && \
    ./configure --host=arm-linux-gnueabihf && \
    make && \
    make install

ENV OPENSSL_LIB_DIR=/usr/local/openssl
ENV OPENSSL_INCLUDE_DIR=/usr/local/openssl/include
ENV OPENSSL_LIB_DIR=/usr/local/openssl
ENV OPENSSL_INCLUDE_DIR=/usr/local/openssl/include
ENV PKG_CONFIG_ALLOW_CROSS=1
ENV PATH="/root/.cargo/bin:$PATH"
ENV CARGO_HOME=/root/.cargo
ENV RUSTUP_HOME=/root/.rustup

RUN curl --proto "=https" --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y \
    && . ~/.cargo/env \
    && echo "source ~/.cargo/env > ~builder/.bash_profile" \
    && rustup target add arm-unknown-linux-gnueabihf \
    && echo "[target.arm-unknown-linux-gnueabihf]\nlinker = \"arm-linux-gnueabihf-gcc\"" > ~/.cargo/config
