FROM ubuntu:20.04
RUN apt-get update && \
apt-get install -y curl build-essential && \
curl https://sh.rustup.rs -sSf | sh -s -- -y
ENV PATH="/root/.cargo/bin:${PATH}"



# Install project dependencies (note this layer gets re-run on every change)

COPY ./properties/Cargo.toml ./Cargo.toml
RUN cargo fetch
COPY ./properties/src ./src
RUN cargo build --release

