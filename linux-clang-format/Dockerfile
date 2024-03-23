FROM ubuntu:20.04
LABEL maintainer="suyu"
# Create a user account suyu (UID 1027) that the container will run as
RUN useradd -m -u 1027 -s /bin/bash suyu && \
    apt-get update && apt-get -y full-upgrade && \
    apt-get install -y git gpg-agent wget && \
    wget https://apt.llvm.org/llvm-snapshot.gpg.key && apt-key add ./llvm-snapshot.gpg.key && \
		wget -qO- https://deb.nodesource.com/setup_20.x | bash - && \
    echo "deb http://apt.llvm.org/focal/ llvm-toolchain-focal-15 main" >> /etc/apt/sources.list && \
    apt-get update && DEBIAN_FRONTEND=noninteractive TZ=Etc/UTC apt-get install -y clang-format-15 nodejs && \
    apt-get clean autoclean && apt-get autoremove --yes && rm -rf /var/lib/apt /var/lib/dpkg /var/lib/cache /var/lib/log
USER 1027
