FROM ubuntu:18.04

MAINTAINER Zach Gray (zgray@scal.io)
LABEL maintainer="Zach Gray <zgray@scal.io>"
LABEL Description="Dockerized Swift for TensorFlow"

ENV DEBIAN_FRONTEND=noninteractive

# Install related packages and set LLVM 3.8 as the compiler
RUN apt-get -q update && \
    apt-get -q install -y \
    make \
    libc6-dev \
    clang-6.0 \
    curl \
    libedit-dev \
    libpython3.6 \
    libpython3.6-dev  \
    libncurses5-dev \
    libicu-dev \
    libssl-dev \
    libxml2 \
    tzdata \
    git \
    libcurl4-openssl-dev \
    pkg-config \
    && update-alternatives --quiet --install /usr/bin/clang clang /usr/bin/clang-6.0 100 \
    && update-alternatives --quiet --install /usr/bin/clang++ clang++ /usr/bin/clang++-6.0 100 \
    && rm -r /var/lib/apt/lists/*

# Install Swift for TensorFlow:

# Define SWIFT_TF_URL as an arg that can be passed by users if needed
ARG SWIFT_TF_URL=https://storage.googleapis.com/swift-tensorflow-artifacts/releases/v0.3/rc1/swift-tensorflow-RELEASE-0.3-ubuntu18.04.tar.gz

RUN rm -rf /usr/lib/python3.7 \
    && curl -fSsL $SWIFT_TF_URL -o swift-tensorflow.tar.gz \
    && tar xzf swift-tensorflow.tar.gz --overwrite --directory / \
    && rm swift-tensorflow.tar.gz

RUN swift --version

RUN cd /usr/src
WORKDIR /usr/src
