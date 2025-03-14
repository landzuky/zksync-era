# For using private GitHub dependencies, CI downdloads all crates and bellman-cuda dependency outside of the contatiner
# Not expected to work locally

# syntax=docker/dockerfile:experimental
FROM debian:buster-slim as builder

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y curl clang openssl libssl-dev gcc g++ \
    pkg-config build-essential libclang-dev && \
    rm -rf /var/lib/apt/lists/*

ENV RUSTUP_HOME=/usr/local/rustup \
    CARGO_HOME=/usr/local/cargo \
    PATH=/usr/local/cargo/bin:$PATH

RUN curl https://sh.rustup.rs -sSf | bash -s -- -y && \
    rustup install nightly-2023-05-31 && \
    rustup default nightly-2023-05-31

WORKDIR /usr/src/zksync
COPY . .

RUN CARGO_HOME=./cargo cargo build --release

FROM debian:buster-slim
RUN apt-get update && apt-get install -y curl openssl libpq5 ca-certificates && rm -rf /var/lib/apt/lists/*
COPY --from=builder /usr/src/zksync/target/release/zksync_circuit_synthesizer /usr/bin/
ENTRYPOINT ["zksync_circuit_synthesizer"]
