# OpenWrt Reticulum Feed 📡

This repository contains OpenWrt packages related to the [Reticulum](https://reticulum.network/) ecosystem. The feed currently provides packages for the [Reticulum Network Stack](https://github.com/markqvist/Reticulum), an open-source cryptography-based networking stack designed for reliable communication over high-latency, low-bandwidth networks.

## Current Status ⚠️

This feed is under active development and not yet ready for general use.

We are working on stabilizing the packages and resolving various implementation challenges, particularly around Python module integration in OpenWrt's constrained environment.

## Available Packages 📦

This feed provides the following packages:

- `rns` The core Reticulum Network Stack implementation with Python dependencies.
- `rnspure` Pure Python variant of the Reticulum Network Stack with no external dependencies required for installation.
- `lxmf` Lightweight Extensible Message Format implementation for messaging over Reticulum.
- `nomadnet` Terminal-based resilient mesh communications platform built on LXMF and Reticulum.
    - `python3-urwid` - Console user interface library required by nomadnet.

Note: The `-src` packages are optional and provide the Python source code, while the standard packages contain only bytecode and metadata as is typical for OpenWrt packages. Most users will only need the standard packages.

## Prerequisites 📋

To use this feed, you need:

- A computer running some Debian
- OpenWrt buildroot environment
- This feed

## Development Setup 🛠️

### System Requirements (Debian/Ubuntu)

Install required system packages:
```bash
sudo apt update && sudo apt install -qy \
    build-essential \
    ccache \
    file \
    flex \
    bison \
    g++ \
    gawk \
    gettext \
    git \
    libncurses5-dev \
    libssl-dev \
    python3-setuptools \
    rsync \
    unzip \
    wget \
    zlib1g-dev
```

### OpenWrt Setup

1. Clone OpenWrt repository:
```bash
git clone --depth 5 https://git.openwrt.org/openwrt/openwrt.git
cd openwrt
```

2. Configure feeds:
```bash
# Create feeds configuration if it doesn't exist
[ ! -f feeds.conf ] && cp feeds.conf.default feeds.conf

# Ensure reticulum feed is properly configured
if ! grep -q '^src-git reticulum' feeds.conf; then
    # Comment out any existing reticulum entries
    sed -i '/src-git reticulum/s/^/#/' feeds.conf
    # Add our feed
    echo "src-git reticulum https://github.com/gretel/feed-reticulum.git" >> feeds.conf
fi
```

Your `feeds.conf` should look similar to this:
```
src-git packages https://git.openwrt.org/feed/packages.git
src-git luci https://git.openwrt.org/project/luci.git
src-git reticulum https://github.com/gretel/feed-reticulum.git
```

3. List using the `feeds` utility to confirm:

```bash
./scripts/feeds list -s
packages   src-git  c01643e78bdf2bcb7de93c7f5408d1c2c4c84aaa https://git.openwrt.org/feed/packages.git
luci       src-git  96c36543311b4120667f463e99a65704f3546a0f https://git.openwrt.org/project/luci.git
reticulum  src-git  39de91edbbff76e84d1672b71a7fc299ff0d6e67 https://github.com/gretel/feed-reticulum.git
```

4. Update and install from all feeds:
```bash
./scripts/feeds update -a
./scripts/feeds install -a
```

### Building Packages

For a default configuration:
```bash
make defconfig
```

To build specific packages:
```bash
# Clean and rebuild RNS package
make package/feeds/reticulum/rns/clean
make package/feeds/reticulum/rns/compile V=s
make package/index
```

> `NO_DEPS=1` will skip building dependencies. Building of the `rns` package will fail if the required dependencies have not been built before. It's meant to speed up development iterations.

> `V=s` adds very verbose output logging. Remove for proven builds.

```bash
make package/feeds/reticulum/rns/compile V=s NO_DEPS=1
```

### Building All

For a full system build (optional):
```bash
make -j$(nproc) world
```

> This could result in a bootable distribution. No testing has been done yet!

## Package Configuration ⚙️

### RNS Configuration

Basic UCI configuration:
```bash
# Enable the service
uci set rns.main.enabled=1
uci commit rns

# Start the service
service rns start
```
Configuration directory: `/var/rns`

Configuration file: `/etc/config/rns`

### LXMF Configuration

Basic UCI configuration:
```bash
# Enable the service
uci set lxmf.main.enabled=1
# Enable propagation node (optional)
uci set lxmf.main.propagation_node=1
uci commit lxmf

# Start the service
service lxmf start
```

Configuration directory: `/var/rns`

Configuration file: `/etc/config/lxmf`

### Nomadnet Configuration

Basic UCI configuration:
```bash
# Enable the service
uci set nomadnet.main.enabled=1
uci commit nomadnet

# Start the service
service nomadnet start
```

Configuration directory: `/var/rns`

Configuration file: `/etc/config/nomadnet`

### Urwid Configuration
No configuration required - library package.

## ToDos ⚡

- [~~Python module import issues related to OpenWrt's bytecode-only packaging~~](https://github.com/markqvist/Reticulum/issues/623)
- ~~Service initialization requires further testing~~
- ~~Documentation needs expansion~~
- ~~Implementation of procd service files needs testing~~
- Ship suitable configuration for `rnsd` and `lxmd`

## Contributing 🤝

We welcome contributions to expand the Reticulum ecosystem on OpenWrt. Please ensure your pull requests are well-documented and include appropriate test cases.

## License ⚖️

Each package is released under its respective license:
- RNS: MIT License
- LXMF: MIT License
- Nomadnet: GPL-3.0
- python3-urwid: LGPL-2.1 or later

## Acknowledgments 🙏

- [Mark Qvist](https://github.com/markqvist) for creating Reticulum Network Stack
- The OpenWrt team
- All contributors to the Reticulum ecosystem