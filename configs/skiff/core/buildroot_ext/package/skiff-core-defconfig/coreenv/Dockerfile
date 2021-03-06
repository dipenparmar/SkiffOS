FROM ubuntu:focal as stage1

# setup environment
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
ENV container docker

# Core packages
RUN export DEBIAN_FRONTEND=noninteractive; \
  apt-get update && \
  apt-get dist-upgrade -y && \
  apt-get install -y  \
  --no-install-recommends \
  -o "Dpkg::Options::=--force-confdef"  \
  -o "Dpkg::Options::=--force-confold"  \
  build-essential \
  openssh-client \
  rsync \
  lsb-release \
  systemd \
  usbutils \
  sudo \
  curl \
  htop \
  git \
  vim \
  nano \
  net-tools \
  software-properties-common \
  unzip \
  autotools-dev \
  cups \
  lightdm \
  xorg \
  xserver-xorg-input-all \
  locales \
  ubuntu-desktop-minimal \
  wget && \
  apt-get autoremove -y && \
  rm -rf /var/lib/apt/lists/*

RUN locale-gen "en_US.UTF-8"

FROM scratch as stage2

COPY --from=stage1 / /

ENV container=docker \
  LANG=en_US.UTF-8 \
  LANGUAGE=en_US:en \
  LC_ALL=en_US.UTF-8

# Create the user 'core' which will be the usual userspace account
# Also allow core to run stuff as sudo without a password.
RUN \
  adduser core \
  --no-create-home \
  --gecos "Core" \
  --shell /bin/bash \
  --disabled-password && \
  adduser core audio && \
  adduser core sudo && \
  adduser core root && \
  adduser core systemd-journal && \
  adduser core dialout && \
  adduser core plugdev && \
  mkdir -p /home/core/ && \
  chown core:core /home/core && \
  echo "core    ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

RUN systemctl set-default graphical.target && \
    systemctl mask tmp.mount && \
    find /etc/systemd/system \
         /lib/systemd/system \
         \( -path '*.wants/*' \
         -name '*swapon*' \
         -or -name '*ntpd*' \
         -or -name '*resolved*' \
         -or -name '*udev*' \
         -or -name '*freedesktop*' \
         -or -name '*persist-resize*' \
         -or -name '*remount-fs*' \
         -or -name '*getty*' \
         -or -name '*systemd-sysctl*' \
         -or -name '*.mount' \
         -or -name '*remote-fs*' \) \
         -exec echo \{} \; \
         -exec rm \{} \;

WORKDIR /
ENTRYPOINT ["/lib/systemd/systemd"]

