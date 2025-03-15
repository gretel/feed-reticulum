include $(TOPDIR)/rules.mk

PKG_NAME:=reticulum
PKG_VERSION:=0.9.3
PKG_RELEASE:=4

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Tom Hensel <code@jitter.eu>

# Common build dependencies
PKG_BUILD_DEPENDS:=python-wheel/host

include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/python/python3-package.mk

# Base package definition (shared code)
define Package/reticulum/Default
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Reticulum
	URL:=https://github.com/markqvist/Reticulum
	DEPENDS:= \
		+python3-asyncio \
		+python3-codecs \
		+python3-ctypes \
		+python3-light \
		+python3-logging \
		+python3-multiprocessing
endef

# Full version with all dependencies
define Package/rns
	$(call Package/reticulum/Default)
	TITLE:=Reticulum Network Stack
	DEPENDS+= \
		+python3-cryptography \
		+python3-pyserial
	CONFLICTS:=rnspure
	VARIANT:=full
endef

# Pure version with minimal dependencies
define Package/rnspure
	$(call Package/reticulum/Default)
	TITLE:=Reticulum Network Stack (Pure Python)
	CONFLICTS:=rns
	VARIANT:=pure
endef

# Source packages
define Package/rns-src
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Reticulum
	TITLE:=Reticulum Network Stack (Source Code)
	URL:=https://github.com/markqvist/Reticulum
	CONFLICTS:=rnspure-src
	VARIANT:=full
endef

define Package/rnspure-src
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Reticulum
	TITLE:=Reticulum Network Stack (Source Code, Pure Python)
	URL:=https://github.com/markqvist/Reticulum
	CONFLICTS:=rns-src
	VARIANT:=pure
endef

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
ifeq ($(BUILD_VARIANT),pure)
	$(CP) $(DL_DIR)/rnspure-$(PKG_VERSION)-py3-none-any.whl $(PKG_BUILD_DIR)/
else
	$(CP) $(DL_DIR)/rns-$(PKG_VERSION)-py3-none-any.whl $(PKG_BUILD_DIR)/
endif
endef

define Build/Compile
ifeq ($(BUILD_VARIANT),pure)
	$(HOST_PYTHON3_BIN) -m installer \
		--destdir "$(PKG_INSTALL_DIR)" \
		--no-compile-bytecode \
		--prefix /usr \
		"$(PKG_BUILD_DIR)/rnspure-$(PKG_VERSION)-py3-none-any.whl"
else
	$(HOST_PYTHON3_BIN) -m installer \
		--destdir "$(PKG_INSTALL_DIR)" \
		--no-compile-bytecode \
		--prefix /usr \
		"$(PKG_BUILD_DIR)/rns-$(PKG_VERSION)-py3-none-any.whl"
endif
endef

# Common installation logic
define Py3Package/Install/Default
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/* $(1)/usr/bin/
	$(SED) '1c#!/usr/bin/python3' $(1)/usr/bin/{rncp,rnid,rnir,rnodeconf,rnpath,rnprobe,rnsd,rnstatus,rnx}

	$(INSTALL_DIR) $(1)$(PYTHON3_PKG_DIR)
	$(CP) $(PKG_INSTALL_DIR)$(PYTHON3_PKG_DIR)/* $(1)$(PYTHON3_PKG_DIR)/

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/rns.init $(1)/etc/init.d/rns
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/rns.config $(1)/etc/config/rns
	$(INSTALL_DATA) ./files/config $(1)/var/rns/.reticulum/
endef

# Installation for each variant
define Py3Package/rns/install
	$(call Py3Package/Install/Default,$(1))
endef

define Py3Package/rnspure/install
	$(call Py3Package/Install/Default,$(1))
endef

# Common descriptions and files
define Package/reticulum/Default/description
Reticulum is the cryptography-based networking stack for building local and wide-area networks with readily available hardware.

It can operate even with very high latency and extremely low bandwidth.

Reticulum allows you to build wide-area networks with off-the-shelf tools, and offers end-to-end encryption and connectivity,
initiator anonymity, autoconfiguring cryptographically backed multi-hop transport, efficient addressing,
unforgeable delivery acknowledgements and more.
endef

# Package specific descriptions
define Package/rns/description
$(call Package/reticulum/Default/description)

This package contains only bytecode and metadata with full functionality.
endef

define Package/rnspure/description
$(call Package/reticulum/Default/description)

This package contains only bytecode and metadata with minimal dependencies ("pure").
endef

define Package/rns-src/description
$(call Package/reticulum/Default/description)

This package adds the Python source code with full functionality.
endef

define Package/rnspure-src/description
$(call Package/reticulum/Default/description)

This package adds the Python source code with minimal dependencies ("pure").
endef

# Common config files and post-install
define Package/reticulum/Default/conffiles
/etc/config/rns
/var/rns/.reticulum/config
endef

define Package/rns/conffiles
$(call Package/reticulum/Default/conffiles)
endef

define Package/rnspure/conffiles
$(call Package/reticulum/Default/conffiles)
endef

define Package/reticulum/Default/postinst
#!/bin/sh
[ -n "$$IPKG_INSTROOT" ] || {
	user_exists rns 142 || user_add rns 142 "Reticulum User" /var/rns /bin/ash
	group_exists rns 142 || group_add rns 142 && group_add_user rns rns
	group_add_user dialout rns
	mkdir -m 0755 -p $(1)/var/rns/.reticulum
	chown -R rns:rns $(1)/var/rns
	[ -d "/lib/upgrade/keep.d" ] || mkdir -p "/lib/upgrade/keep.d/"
	echo "/var/rns/.reticulum" > "/lib/upgrade/keep.d/rns"
}
endef

define Package/rns/postinst
$(call Package/reticulum/Default/postinst)
endef

define Package/rnspure/postinst
$(call Package/reticulum/Default/postinst)
endef

# Download definitions for each variant
define Download/rns
	URL:=https://github.com/markqvist/Reticulum/releases/download/$(PKG_VERSION)
	FILE:=rns-$(PKG_VERSION)-py3-none-any.whl
	HASH:=0270c988a2b898b28348cd78138667115d4ef3f7e09c86531baaefbee35ef851
endef
$(eval $(call Download,rns))

define Download/rnspure
	URL:=https://github.com/markqvist/Reticulum/releases/download/$(PKG_VERSION)
	FILE:=rnspure-$(PKG_VERSION)-py3-none-any.whl
	HASH:=eee1a6c4c9c0f04bb17b12b8fb37b9c4cec12a99c87a046730eb7c9a6ffd999f
endef
$(eval $(call Download,rnspure))

# Evaluate all package definitions
$(eval $(call Py3Package,rns))
$(eval $(call Py3Package,rnspure))
$(eval $(call BuildPackage,rns))
$(eval $(call BuildPackage,rnspure))
$(eval $(call BuildPackage,rns-src))
$(eval $(call BuildPackage,rnspure-src))