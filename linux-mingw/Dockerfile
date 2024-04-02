FROM archlinux:latest
LABEL maintainer="suyu"

# Add mingw-repo "ownstuff" is a AUR with an up to date mingw64
# Runs pacman -Syu twice in order to work around pacman issues where the first run only updates the
# current distro packages, and the second run actually pulls the updates from the repos.
RUN useradd -m -u 1027 -s /bin/bash suyu && mkdir -p /tmp/pkgs && \
    echo "[ownstuff]" >> /etc/pacman.conf && \
    echo "SigLevel = Optional TrustAll" >> /etc/pacman.conf && \
    echo "Server = https://martchus.no-ip.biz/repo/arch/ownstuff/os/\$arch" >> /etc/pacman.conf && \
    pacman -Syu --noconfirm && \
    pacman -Syu --noconfirm && \
    pacman -S --needed --noconfirm --noprogressbar \
    base-devel \
    clang \
    polly \
    lld \
    sudo \
    gnupg \
    wget \
    git \
    glslang \
# TODO: Remove python-pip after a reasonable amount of time has passed to let suyu PRs rebase its scripts
    python-pefile \
    python-pip \
    python \
    ccache \
    p7zip \
    cmake \
    ninja \
    mingw-w64-gcc \
    mingw-w64-libusb \
    mingw-w64-lz4 \
    mingw-w64-nlohmann-json \
    mingw-w64-opus \
    mingw-w64-qt5-base \
    mingw-w64-qt5-declarative \
    mingw-w64-qt5-graphicaleffects \
    mingw-w64-qt5-multimedia \
    mingw-w64-qt5-tools \
    mingw-w64-qt5-winextras \
    mingw-w64-tools \
    mingw-w64-winpthreads \
    mingw-w64-zlib \
    mingw-w64-zstd \
    mingw-w64-cmake \
    mingw-w64-sdl2 \
		mingw-w64-boost \
		nodejs-lts-iron \
    && \
    pacman -Scc --noconfirm && \
    rm -rf /usr/share/man/ /tmp/* /var/tmp/ /usr/{i686-w64-mingw32,lib32} /usr/lib/gcc/i686-w64-mingw32

# Compatibility with the old Ubuntu MingW image
RUN ln -s /usr/x86_64-w64-mingw32/lib/qt /usr/x86_64-w64-mingw32/lib/qt5
# Give suyu user sudo access for AUR usage
RUN echo "suyu ALL=(ALL:ALL) NOPASSWD: ALL" >> /etc/sudoers

USER 1027
# Build/install small AUR packages
RUN \
# mingw-w64-clang -- MinGW wrapper for host clang
    cd && \
    git clone https://aur.archlinux.org/mingw-w64-wclang-git.git && \
    cd mingw-w64-wclang-git && \
    makepkg -si --noconfirm --noprogressbar && \
# mingw-w64-fmt
    cd && \
    git clone https://aur.archlinux.org/mingw-w64-fmt.git && \
    cd mingw-w64-fmt && \
    # Only build for x86_64
    sed -i 's/i686-w64-mingw32//g' PKGBUILD && \
    makepkg -si --noconfirm --noprogressbar --nocheck && \
# cleanup
    cd && rm -rf mingw-* && \
    sudo sed -i 's/suyu/#suyu/g' /etc/sudoers

