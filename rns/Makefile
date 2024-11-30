include $(TOPDIR)/rules.mk

PKG_NAME:=rns
PKG_VERSION:=0.8.6
PKG_RELEASE:=3

PKG_HASH:=9ddcf02f16f531edaa22a8b1d41f9888ce4ef429d14131d939c6917f5a957784
PKG_SOURCE:=rns-$(PKG_VERSION)-py3-none-any.whl
PKG_SOURCE_URL:=https://github.com/gretel/Reticulum/releases/download/0.9.7-beta
PKG_BUILD_DEPENDS:=python-wheel/host

PKG_LICENSE:=GPL
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Tom Hensel <code@jitter.eu>

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
		+python3-light \
		+python3-cryptography \
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
/etc/config/rnsd
endef

define Package/rns/postinst
#!/bin/sh
[ -n "$$IPKG_INSTROOT" ] || {
	# create reticulum user and group
	group_exists rnsd || group_add rnsd 142
	user_exists rnsd || user_add rnsd 142 142 "Reticulum daemon" /var/run/rnsd

	# ensure correct permissions on directory
	[ -d /var/run/rnsd ] || {
		mkdir -m 0755 -p /var/run/rnsd
		chown rnsd:rnsd /var/run/rnsd
	}

	# enable and start service
	/etc/init.d/rnsd enable
	/etc/init.d/rnsd start
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
	$(INSTALL_BIN) ./files/rnsd.init $(1)/etc/init.d/rnsd
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/rnsd.config $(1)/etc/config/rnsd
endef

$(eval $(call Py3Package,rns))
$(eval $(call BuildPackage,rns))
$(eval $(call BuildPackage,rns-src))
