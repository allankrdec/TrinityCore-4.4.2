# -------- STAGE 1: BUILD --------
FROM ubuntu:24.04 AS builder

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git clang cmake make gcc g++ \
    libmysqlclient-dev libssl-dev libbz2-dev \
    libreadline-dev libncurses-dev libboost-all-dev \
    mysql-server p7zip \
    && rm -rf /var/lib/apt/lists/*

RUN update-alternatives --install /usr/bin/cc cc /usr/bin/clang 100 && \
    update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang 100

WORKDIR /build

RUN git clone https://github.com/allankrdec/TrinityCore-4.4.2.git

WORKDIR /build/TrinityCore-4.4.2
RUN mkdir build

WORKDIR /build/TrinityCore-4.4.2/build

RUN cmake .. -DNOJEM=1 -DTOOLS=1 -DSERVERS=1 -DCMAKE_INSTALL_PREFIX=/app && \
    make -j$(nproc) && \
    make install


# -------- STAGE 2: RUNTIME --------
FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    libmysqlclient21 libssl3 libbz2-1.0 \
    libreadline8 libncurses6 \
    libboost-all-dev \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y netcat-openbsd
RUN apt-get update && apt-get install -y mysql-client

COPY --from=builder /app /app

WORKDIR /app

EXPOSE 8085 3724
