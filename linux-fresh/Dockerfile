FROM ubuntu:20.04
LABEL maintainer="suyu"

ENV CCACHE_VER=4.9.1
ENV BOOST_VER=1_79_0
ENV CLANG_VER=14
ENV CMAKE_VER=3.22.6
ENV DEBIAN_FRONTEND=noninteractive
ENV GCC_VER=12.2.0
ENV GNU_BIN_VER=2.40
ENV QT_PKG_VER=515
ENV QT_VER=5.15.2
ENV UBUNTU_VER=focal

# Create a user account suyu (UID 1027) that the container will run as
RUN useradd -m -u 1027 -s /bin/bash suyu && \
    apt-get update && \
    apt-get full-upgrade -y && \
    apt-get install --no-install-recommends -y \
    apt-utils \
    ca-certificates \
    gnupg \
    software-properties-common \
    unzip \
    wget \
    xz-utils \
# suyu build requirements
    build-essential \
    git \
    libgl-dev \
    liblz4-dev \
    libpulse-dev \
    libudev-dev \
    libssl-dev \
    libtool \
    libwayland-dev \
    ninja-build \
    pkg-config \
    zlib1g-dev \
# AppImage support
    appstream \
    desktop-file-utils \
    file \
    libfile-mimeinfo-perl \
    patchelf \
    zsync \
# FFmpeg build requirements
    libdrm-dev \
    libva-dev \
    libx11-dev \
    libxext-dev \
    nasm \
# libusb build requirements
    autoconf \
    automake \
    libtool \
    libudev-dev \
# apt.llvm.org Clang requirements
    gpg-agent \
# vcpkg requirements
    curl \
    zip \
    && \
# Install updated versions of glslang, git, and Qt from launchpad repositories
    add-apt-repository -y ppa:beineri/opt-qt-${QT_VER}-${UBUNTU_VER} && \
    add-apt-repository -y ppa:savoury1/graphics && \
    add-apt-repository -y ppa:savoury1/multimedia && \
    add-apt-repository -y ppa:savoury1/ffmpeg4 && \
    add-apt-repository -y ppa:git-core/ppa && \
    apt-get update -y && \
    apt-get install --no-install-recommends -y \
    git \
    glslang-dev \
    glslang-tools \
    libhidapi-dev \
    qt${QT_PKG_VER}base \
    qt${QT_PKG_VER}tools \
    qt${QT_PKG_VER}wayland \
    qt${QT_PKG_VER}multimedia \
    qt${QT_PKG_VER}x11extras && \
# Install Clang from apt.llvm.org
    wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add - && \
    echo "deb http://apt.llvm.org/${UBUNTU_VER}/ llvm-toolchain-${UBUNTU_VER}-${CLANG_VER} main" >> /etc/apt/sources.list && \
    apt-get update -y && \
    apt-get install --no-install-recommends -y \
    clang-${CLANG_VER} \
    lld-${CLANG_VER} \
    llvm-${CLANG_VER} \
    llvm-${CLANG_VER}-linker-tools && \
    ln -s $(which clang-${CLANG_VER}) /usr/bin/clang && \
    ln -s $(which clang++-${CLANG_VER}) /usr/bin/clang++ && \
    dpkg-reconfigure ccache && \
# Install NodeJS
		wget -qO- https://deb.nodesource.com/setup_20.x | bash - && apt-get install -y nodejs && \
# Clean Container
    apt-get clean autoclean && \
    apt-get autoremove --yes && \
    rm -rf /var/lib/apt /var/lib/dpkg /var/lib/cache /var/lib/log

# Install CMake from upstream
RUN cd /tmp && \
    wget --no-verbose https://github.com/Kitware/CMake/releases/download/v${CMAKE_VER}/cmake-${CMAKE_VER}-linux-x86_64.tar.gz && \
    tar xvf cmake-${CMAKE_VER}-linux-x86_64.tar.gz && \
    cp -rv cmake-${CMAKE_VER}-linux-x86_64/* /usr && \
    rm -rf cmake-*

# Install Boost from suyu/ext-linux-bin
RUN cd /tmp && \
    wget --no-verbose https://git.suyu.dev/suyu/ext-linux-bin/raw/main/boost/boost-${BOOST_VER}.tar.xz && \
    tar xvf boost-${BOOST_VER}.tar.xz && \
    chown -R root:root boost-${BOOST_VER}/ && \
    cp -rv boost-${BOOST_VER}/usr / && \
    rm -rf boost*

# Install GCC from suyu/ext-linux-bin
RUN cd /tmp && \
    wget --no-verbose \
        https://git.suyu.dev/suyu/ext-linux-bin/raw/main/gcc/gcc-${GCC_VER}-ubuntu.tar.xz.aa \
        https://git.suyu.dev/suyu/ext-linux-bin/raw/main/gcc/gcc-${GCC_VER}-ubuntu.tar.xz.ab \
        https://git.suyu.dev/suyu/ext-linux-bin/raw/main/gcc/gcc-${GCC_VER}-ubuntu.tar.xz.ac \
        https://git.suyu.dev/suyu/ext-linux-bin/raw/main/gcc/gcc-${GCC_VER}-ubuntu.tar.xz.ad && \
    cat gcc-${GCC_VER}-ubuntu.tar.xz.* | tar xJ && \
    cp -rv gcc-${GCC_VER}/usr / && \
    rm -rf /tmp/gcc* && \
# Use updated libstdc++ and libgcc_s on the container from GCC 11
    rm -v /usr/lib/x86_64-linux-gnu/libstdc++.so.6 /lib/x86_64-linux-gnu/libgcc_s.so.1 && \
    ln -sv /usr/local/lib64/libstdc++.so.6.0.30 /usr/lib/x86_64-linux-gnu/libstdc++.so.6 && \
    ln -sv /usr/local/lib64/libgcc_s.so.1 /lib/x86_64-linux-gnu/libgcc_s.so.1 && \
# Help Clang find the updated GCC C++ version
    ln -sv /usr/local/include/c++/${GCC_VER}/ /usr/include/c++/${GCC_VER} && \
    ln -sv /usr/local/lib/gcc/x86_64-pc-linux-gnu/${GCC_VER} /usr/lib/gcc/x86_64-linux-gnu/${GCC_VER} && \
    cp -rv /usr/local/include/c++/${GCC_VER}/x86_64-pc-linux-gnu/* /usr/local/include/c++/${GCC_VER}/

# Install GNU binutils from suyu/ext-linux-bin
RUN cd /tmp && \
    wget --no-verbose \
        https://git.suyu.dev/suyu/ext-linux-bin/raw/main/binutils/binutils-${GNU_BIN_VER}-${UBUNTU_VER}.tar.xz && \
    tar xf binutils-${GNU_BIN_VER}-${UBUNTU_VER}.tar.xz && \
    cp -rv binutils-${GNU_BIN_VER}-${UBUNTU_VER}/usr / && \
    rm -rf /tmp/binutils*

# Install CCache from suyu/ext-linux-bin
RUN cd /tmp && \
    wget --no-verbose https://git.suyu.dev/suyu/ext-linux-bin/raw/main/ccache/ccache-${CCACHE_VER}-linux-x86_64.tar.xz && \
    tar xvf ccache-${CCACHE_VER}-linux-x86_64.tar.xz && \
    chown -R root:root ccache-${CCACHE_VER}-linux-x86_64/ && \
    cp -rv ccache-${CCACHE_VER}-linux-x86_64/usr / && \
    rm -rf ccache*

# Setup paths for Qt binaries
ENV LD_LIBRARY_PATH=/opt/qt${QT_PKG_VER}/lib:${LD_LIBRARY_PATH}
ENV PATH=/opt/qt${QT_PKG_VER}/bin:${PATH}

# Fix GCC 11 <-> Qt 5.15 issue
COPY qtconcurrentthreadengine.patch /opt/qt515/qtconcurrentthreadengine.patch
RUN patch /opt/qt515/include/QtConcurrent/qtconcurrentthreadengine.h /opt/qt515/qtconcurrentthreadengine.patch && \
    rm /opt/qt515/qtconcurrentthreadengine.patch

# Tell CMake to use vcpkg when looking for packages
ENV VCPKG_TOOLCHAIN_FILE=/home/suyu/vcpkg/scripts/buildsystems/vcpkg.cmake

USER 1027
# Install vcpkg and required dependencies for suyu
RUN cd /home/suyu &&\
    git clone --depth 1 https://github.com/Microsoft/vcpkg.git &&\
    cd vcpkg &&\
    ./bootstrap-vcpkg.sh &&\
    ./vcpkg install \
        catch2 \
        fmt \
        lz4 \
        nlohmann-json \
        zlib \
        zstd
