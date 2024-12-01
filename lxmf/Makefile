include $(TOPDIR)/rules.mk

PKG_NAME:=lxmf
PKG_VERSION:=0.5.8
PKG_RELEASE:=1

PKG_HASH:=70005cd418970c683b7b8d96e446d30ecfe790032310b3252247bb7ea576ab1f
PKG_SOURCE:=lxmf-$(PKG_VERSION)-py3-none-any.whl
PKG_SOURCE_URL:=https://github.com/markqvist/LXMF/releases/download/0.5.8
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

define Package/lxmf
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Reticulum
	TITLE:=Lightweight Extensible Message Format
	URL:=https://github.com/markqvist/LXMF
	DEPENDS:= \
		+lxmf \
		+python3-light \
		+python3-qrcode \
		+python3-setuptools
endef

define Package/lxmf-src
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Reticulum
	TITLE:=Lightweight Extensible Message Format (Source Code)
	URL:=https://github.com/markqvist/LXMF
endef

define Package/lxmf/description
	LXMF is a simple and flexible messaging format and delivery protocol that allows
	a wide variety of implementations, while using as little bandwidth as possible.
	It is built on top of Reticulum and offers zero-conf message routing, end-to-end encryption
	and Forward Secrecy, and can be transported over any kind of medium that Reticulum supports.

	This package contains only bytecode and metadata.
endef

define Package/lxmf-src/description
	LXMF is a simple and flexible messaging format and delivery protocol that allows
	a wide variety of implementations, while using as little bandwidth as possible.
	It is built on top of Reticulum and offers zero-conf message routing, end-to-end encryption
	and Forward Secrecy, and can be transported over any kind of medium that Reticulum supports.

	This package adds the Python source code.
endef

define Package/lxmf/conffiles
/etc/config/lxmf
endef

define Package/lxmf/postinst
#!/bin/sh
[ -n "$$IPKG_INSTROOT" ] || {
	# create user and group
	group_exists lxmf || group_add lxmf 143
	user_exists lxmf || user_add lxmf 143 143 "LXMF daemon" /var/run/lxmf

	# ensure correct permissions on directory
	[ -d /var/run/lxmf ] || {
		mkdir -m 0755 -p /var/run/lxmf
		chown lxmf:lxmf /var/run/lxmf
	}

	# enable and start service
	/etc/init.d/lxmf enable
	/etc/init.d/lxmf start
}
endef

define Py3Package/lxmf/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/* $(1)/usr/bin/
	# Fix shebangs
	$(SED) '1c#!/usr/bin/python3' $(1)/usr/bin/lxmd

	# Ensure Python package is installed
	$(INSTALL_DIR) $(1)$(PYTHON3_PKG_DIR)
	$(CP) $(PKG_INSTALL_DIR)$(PYTHON3_PKG_DIR)/* $(1)$(PYTHON3_PKG_DIR)/

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/lxmf.init $(1)/etc/init.d/lxmf
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/lxmf.config $(1)/etc/config/lxmf
endef

$(eval $(call Py3Package,lxmf))
$(eval $(call BuildPackage,lxmf))
$(eval $(call BuildPackage,lxmf-src))
