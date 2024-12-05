# OpenWrt Reticulum Feed 📡

This repository contains OpenWrt packages related to the [Reticulum](https://github.com/markqvist/Reticulum) ecosystem. The feed currently provides packages for the [Reticulum Network Stack](https://github.com/markqvist/Reticulum), an open-source cryptography-based networking stack designed for reliable communication over high-latency, low-bandwidth networks.

## Current Status ⚠️

This feed is under active development and not yet ready for general use.

We are working on stabilizing the packages and resolving various implementation challenges, particularly around Python module integration in OpenWrt's constrained environment.

## Available Packages 📦

![image](https://github.com/user-attachments/assets/3b2f2e90-69d5-4ab3-956a-75205ef3fdcf)

Currently, this feed provides the following packages:

### rns & rns-src 🌐
The core Reticulum Network Stack implementation package. Features:
- Full Reticulum Network Stack implementation
- OpenWrt UCI integration for configuration management
- Proper system integration with procd init scripts
- Dedicated system user and group for security
- Automatic service management
- Source code package available separately

### lxmf & lxmf-src 💬
Lightweight Extensible Message Format implementation. Features:
- Built on top of Reticulum Network Stack
- Zero-configuration message routing
- End-to-end encryption with Forward Secrecy
- Compatible with all transport mediums supported by Reticulum
- Source code package available separately

### python3-urwid 🖥️
A console user interface library dependency:
- Provides text-based user interface capabilities
- Required by some Reticulum ecosystem applications
- Cross-platform compatibility

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
packages   src-git  2b999558db0711124f7b5cf4afa201557352f694 https://git.openwrt.org/feed/packages.git
luci       src-git  e76155d09484602e2b02e84bb8ffafa4848798f0 https://git.openwrt.org/project/luci.git
reticulum  src-git  3d196e9b824158b9a428892c426e8365dc02c373 https://github.com/gretel/feed-reticulum.git
```

4. Update and install from all feeds:
```bash
./scripts/feeds update -a
./scripts/feeds install -a
```

### Configure Target Platform

Before building, configure your target platform/architecture:

```bash
make menuconfig
```

Key configuration areas:
1. Target System (e.g., `x86`, `MediaTek Ralink MIPS`, `Qualcomm Atheros AR7xxx/AR9xxx`)
2. Subtarget (e.g., `x86_64`, `MT7621`, `generic`)
3. Target Profile (specific device or generic profile)

Common platforms include:
```
Target System                     Subtarget         Example Devices
────────────────────────────────────────────────────────────────────
MediaTek Ralink MIPS   (ramips)  MT7621            Ubiquiti EdgeRouter X
Qualcomm Atheros AR7xxx         generic            GL.iNet GL-AR750S
x86                             x86_64             Generic PC/VM
Raspberry Pi                    bcm27xx            Raspberry Pi 4
```

**Select at minimum**:
- Target System
- Subtarget
- Target Profile (if building for specific device)
- Package `rns` under `Network -> Reticulum`

Save your configuration and exit menuconfig.

> Backup `.config` file. It get's overwritten easily.

### Building Packages

For a basic configuration:
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
Configuration directory: `/var/run/rns`

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

Configuration directory: `/var/run/rns`

Configuration file: `/etc/config/lxmf`

### Urwid Configuration
No configuration required - library package.

## Known Issues ⚡

- [~~Python module import issues related to OpenWrt's bytecode-only packaging~~](https://github.com/markqvist/Reticulum/issues/623)
- ~~Service initialization requires further testing~~
- Documentation needs expansion
- `nomadnet` package not done yet, but `urwid` is prepared.

## Contributing 🤝

We welcome contributions to expand the Reticulum ecosystem on OpenWrt. Please ensure your pull requests are well-documented and include appropriate test cases.

## Feed Structure 📁

The feed follows OpenWrt's standard directory structure:
```
feed-reticulum/
├── lxmf/               # LXMF package
│   ├── Makefile
│   └── files/
│       ├── lxmf.init
│       └── lxmf.config
├── rns/                # Reticulum Network Stack package
│   ├── Makefile
│   └── files/
│       ├── rns.init
│       └── rns.config
├── python3-urwid/      # urwid UI library package
│   └── Makefile
├── LICENSE
└── README.md
```

## License ⚖️

Each package is released under its respective license:
- RNS: MIT License
- LXMF: MIT License
- python3-urwid: LGPL-2.1 or later

## Acknowledgments 🙏

- [Mark Qvist](https://github.com/markqvist) for creating Reticulum Network Stack
- The OpenWrt team
- All contributors to the Reticulum ecosystem