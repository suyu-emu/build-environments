FROM ubuntu:jammy
LABEL maintainer="suyu"
ENV ANDROID_HOME=/usr/lib/android-sdk/

RUN useradd -m -u 1027 -s /bin/bash suyu \
		&& apt-get update && apt-get -y full-upgrade \
		&& apt-get install -y git gpg-agent wget \
		&& wget -qO- https://deb.nodesource.com/setup_20.x | bash - \
    && DEBIAN_FRONTEND=noninteractive TZ=Etc/UTC apt-get install -y ccache apksigner glslang-dev glslang-tools git sdkmanager android-sdk bash openjdk-17-jdk openjdk-17-jre curl zip unzip tar cmake ninja-build pkg-config nodejs \
    && DEBIAN_FRONTEND=noninteractive TZ=Etc/UTC apt-get install -y --no-install-recommends clang-14 lld-14 llvm-14 llvm-14-linker-tools \
    && ln -s $(which clang-14) /usr/bin/clang && ln -s $(which clang++-14) /usr/bin/clang++ \
    && echo y | sdkmanager --sdk_root=/usr/lib/android-sdk --licenses \
		&& apt-get clean autoclean && apt-get autoremove --yes && rm -rf /var/lib/apt /var/lib/dpkg /var/lib/cache /var/lib/log
#Disable user for now.
#USER 1027

