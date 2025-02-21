# Use Ubuntu 16.04 as base image according to README
FROM ubuntu:16.04

# Avoid timezone prompt during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Install required packages
RUN apt-get update && apt-get install -y \
    build-essential \
    libev-dev \
    libjansson-dev \
    libmpdec-dev \
    libssl-dev \
    libcurl4-openssl-dev \
    libmysqlclient-dev \
    liblz4-dev \
    pkg-config \
    python \
    && rm -rf /var/lib/apt/lists/*


# Create log directory referenced in config.json
RUN mkdir -p /var/log/trade/marketprice

# Create working directory
WORKDIR /app

# Copy source files
COPY marketprice/ ./marketprice/
COPY network/ ./network/
COPY utils/ ./utils/
COPY depends/ ./depends/
COPY makefile.inc ./makefile.inc

# Build and install hiredis from depends directory
WORKDIR /app/depends/hiredis
RUN make && make install && ldconfig

# Build and install librdkafka from depends directory
WORKDIR /app/depends/librdkafka
RUN ./configure && make && make install && ldconfig


# Build network library
WORKDIR /app/network
RUN make

# Build utils library
WORKDIR /app/utils
RUN make

# Build the application
WORKDIR /app/marketprice
RUN make

# Set default command
CMD ["./marketprice.exe", "config.json"]