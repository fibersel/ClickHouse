# docker build -t yandex/clickhouse-binary-builder .
FROM ubuntu:18.04

RUN apt-get --allow-unauthenticated update -y \
    && env DEBIAN_FRONTEND=noninteractive \
        apt-get --allow-unauthenticated install --yes --no-install-recommends \
            bash \
            fakeroot \
            ccache \
            curl \
            software-properties-common \
            gnupg \
            apt-transport-https \
            ca-certificates

RUN echo "deb [trusted=yes] http://apt.llvm.org/bionic/ llvm-toolchain-bionic-7 main" >> /etc/apt/sources.list
RUN echo "deb [trusted=yes] http://apt.llvm.org/bionic/ llvm-toolchain-bionic-8 main" >> /etc/apt/sources.list
RUN add-apt-repository ppa:ubuntu-toolchain-r/test

RUN apt-get update -y \
    && env DEBIAN_FRONTEND=noninteractive \
        apt-get install --yes --no-install-recommends \
            bash \
            cmake \
            ccache \
            curl \
            gcc-7 \
            g++-7 \
            gcc-8 \
            g++-8 \
            gcc-9 \
            g++-9 \
            clang-6.0 \
            lld-6.0 \
            libclang-6.0-dev \
            liblld-6.0-dev \
            llvm-6.0 \
            libllvm6.0 \
            llvm-6.0-dev \
            clang-7 \
            lld-7 \
            libclang-7-dev \
            liblld-7-dev \
            clang-8 \
            lld-8 \
            libclang-8-dev \
            liblld-8-dev \
            libicu-dev \
            libreadline-dev \
            ninja-build \
            gperf \
            git \
            tzdata \
            gperf \
            cmake \
            gdb \
            rename \
            wget

ENV CC=clang-8
ENV CXX=clang++-8

# libtapi is required to support .tbh format from recent MacOS SDKs
RUN git clone https://github.com/tpoechtrager/apple-libtapi.git
RUN cd apple-libtapi && INSTALLPREFIX=/cctools ./build.sh && ./install.sh
RUN rm -rf apple-libtapi

# Build and install tools for cross-linking to Darwin
RUN git clone https://github.com/tpoechtrager/cctools-port.git
RUN cd cctools-port/cctools && ./configure --prefix=/cctools --with-libtapi=/cctools --target=x86_64-apple-darwin && make install
RUN rm -rf cctools-port

# Download toolchain for Darwin
RUN mkdir -p /build/cmake/toolchain/darwin-x86_64
RUN wget https://github.com/phracker/MacOSX-SDKs/releases/download/10.14-beta4/MacOSX10.14.sdk.tar.xz
RUN tar xJf MacOSX10.14.sdk.tar.xz -C /build/cmake/toolchain/darwin-x86_64 --strip-components=1

# Download toolchain for ARM
RUN mkdir -p /build/cmake/toolchain/linux-aarch64
RUN wget "https://developer.arm.com/-/media/Files/downloads/gnu-a/8.3-2019.03/binrel/gcc-arm-8.3-2019.03-x86_64-aarch64-linux-gnu.tar.xz?revision=2e88a73f-d233-4f96-b1f4-d8b36e9bb0b9&la=en" -O gcc-arm-8.3-2019.03-x86_64-aarch64-linux-gnu.tar.xz
RUN tar xJf gcc-arm-8.3-2019.03-x86_64-aarch64-linux-gnu.tar.xz -C /build/cmake/toolchain/linux-aarch64 --strip-components=1

COPY build.sh /
CMD ["/bin/bash", "/build.sh"]
