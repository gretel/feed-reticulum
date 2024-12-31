include $(TOPDIR)/rules.mk

PKG_NAME:=rns
PKG_VERSION:=0.8.8
PKG_RELEASE:=5

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Tom Hensel <code@jitter.eu>

PKG_HASH:=98d2dd23414db57078b2a4346cdf1a5cb15fe7120ede5627373b17114e21c3aa
PKG_SOURCE:=rns-$(PKG_VERSION)-py3-none-any.whl
PKG_SOURCE_URL:=https://github.com/gretel/Reticulum/releases/download/0.8.8-alpha1

PKG_BUILD_DEPENDS:=python-wheel/host

PYTHON3_PKG_FORCE_DISTUTILS_SETUP:=1

include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/python/python3-package.mk

define Build/Prepare
		$(CP) $(DL_DIR)/$(PKG_SOURCE) $(PKG_BUILD_DIR)/
endef

define Build/Compile
		$(HOST_PYTHON3_BIN) -m installer \
				--destdir "$(PKG_INSTALL_DIR)" \
				--no-compile-bytecode \
				--prefix /usr \
				"$(PKG_BUILD_DIR)/$(PKG_SOURCE)"
endef

define Package/rns
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Reticulum
	TITLE:=Reticulum Network Stack
	URL:=https://github.com/markqvist/Reticulum
	DEPENDS:= \
		+python3-asyncio \
		+python3-codecs \
		+python3-cryptography \
		+python3-ctypes \
		+python3-light \
		+python3-logging \
		+python3-multiprocessing \
		+python3-pyserial
endef

define Package/rns-src
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Reticulum
	TITLE:=Reticulum Network Stack (Source Code)
	URL:=https://github.com/markqvist/Reticulum
endef

define Package/rns/description
	Reticulum is the cryptography-based networking stack for building local and wide-area networks with readily available hardware.

	It can operate even with very high latency and extremely low bandwidth.

	Reticulum allows you to build wide-area networks with off-the-shelf tools, and offers end-to-end encryption and connectivity,
	initiator anonymity, autoconfiguring cryptographically backed multi-hop transport, efficient addressing,
	unforgeable delivery acknowledgements and more.

	This package contains only bytecode and metadata.
endef

define Package/rns-src/description
	Reticulum is the cryptography-based networking stack for building local and wide-area networks with readily available hardware.

	It can operate even with very high latency and extremely low bandwidth.

	Reticulum allows you to build wide-area networks with off-the-shelf tools, and offers end-to-end encryption and connectivity,
	initiator anonymity, autoconfiguring cryptographically backed multi-hop transport, efficient addressing,
	unforgeable delivery acknowledgements and more.

	This package adds the Python source code.
endef

define Package/rns/conffiles
/etc/config/rns
endef

define Package/rns/postinst
#!/bin/sh
[ -n "$$IPKG_INSTROOT" ] || {
	# Create user and group
	group_exists rns 142 || group_add rns 142
	user_exists rns 142 || user_add rns 142 142 "Reticulum daemon" /var/rns

	# Allow accessing serial ports
	addgroup rns dialout

	# Preserve on upgrade
	[ -d "/lib/upgrade/keep.d" ] || mkdir -p "/lib/upgrade/keep.d/"
	echo "/var/rns/" > "/lib/upgrade/keep.d/rns"
}
endef

define Py3Package/rns/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/* $(1)/usr/bin/
	# Fix shebangs
	$(SED) '1c#!/usr/bin/python3' $(1)/usr/bin/{rncp,rnid,rnir,rnodeconf,rnpath,rnprobe,rnsd,rnstatus,rnx}

	# Ensure Python package is installed
	$(INSTALL_DIR) $(1)$(PYTHON3_PKG_DIR)
	$(CP) $(PKG_INSTALL_DIR)$(PYTHON3_PKG_DIR)/* $(1)$(PYTHON3_PKG_DIR)/

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/rns.init $(1)/etc/init.d/rns
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/rns.config $(1)/etc/config/rns
endef

$(eval $(call Py3Package,rns))
$(eval $(call BuildPackage,rns))
$(eval $(call BuildPackage,rns-src))
