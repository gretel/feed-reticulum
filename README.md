# OpenWrt Reticulum Feed

This repository contains OpenWrt packages related to the Reticulum ecosystem. The feed currently provides packages for the [Reticulum Network Stack](https://github.com/markqvist/Reticulum), an open-source cryptography-based networking stack designed for reliable communication over high-latency, low-bandwidth networks.

## Current Status

⚠️ This feed is under active development and not yet ready for general use.

We are working on stabilizing the packages and resolving various implementation challenges, particularly around Python module integration in OpenWrt's constrained environment.

## Available Packages

![image](https://github.com/user-attachments/assets/3b2f2e90-69d5-4ab3-956a-75205ef3fdcf)

Currently, this feed provides the following packages:

### rns
The core Reticulum Network Stack implementation package. It provides:
- Full Reticulum Network Stack implementation
- OpenWrt UCI integration for configuration management
- Proper system integration with procd init scripts
- Dedicated system user and group for security
- Automatic service management

### rns-src
Source code package for Reticulum Network Stack, complementing the bytecode-only main package.

## Prerequisites

To use this feed, you need:
- OpenWrt buildroot environment
- Python 3.11 or later
- Required Python packages: cryptography, pyserial

## Installation

First, add this feed to your OpenWrt buildroot:

```bash
# In your OpenWrt root directory
cp feeds.conf.default feeds.conf
echo "src-git reticulum https://github.com/gretel/feed-reticulum.git" >> feeds.conf
./scripts/feeds update reticulum
./scripts/feeds install -p reticulum -a
```

Then enable desired packages in your build configuration:

```bash
make menuconfig
# Navigate to Network -> Reticulum
```

## Package Configuration

### rns Package

After installation, the RNS service can be configured through UCI:

```bash
# Enable the service
uci set rnsd.main.enabled=1
uci commit rnsd

# Start the service
service rnsd start
```

Runtime configuration will be created in `/var/run/rnsd` upon first service start.

## Known Issues

- Python module import issues related to OpenWrt's bytecode-only packaging
- Service initialization requires further testing
- Documentation needs expansion

## Contributing

We welcome contributions to expand the Reticulum ecosystem on OpenWrt. Please ensure your pull requests are well-documented and include appropriate test cases.

## Feed Structure

The feed follows OpenWrt's standard directory structure:
```
feed-reticulum/
├── net/
│   └── rns/              # Reticulum Network Stack package
│       ├── Makefile
│       └── files/
│           ├── rnsd.init
│           └── rnsd.config
├── LICENSE
└── README.md
```

## License

This feed is released under the GNU General Public License v2.

## Acknowledgments

- [Mark Qvist](https://github.com/markqvist) for creating the Reticulum Network Stack
- The OpenWrt team for their excellent embedded Linux distribution
