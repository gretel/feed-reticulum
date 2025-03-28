include $(TOPDIR)/rules.mk

PKG_NAME:=nomadnet
PKG_VERSION:=0.6.1
PKG_RELEASE:=7

PKG_LICENSE:=GNU
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Tom Hensel <code@jitter.eu>

PKG_HASH:=70b5f3fa481257128c4c475738bd4f9a63380c4661714e33dca0215408fad523
PKG_SOURCE:=nomadnet-$(PKG_VERSION)-py3-none-any.whl
PKG_SOURCE_URL:=https://github.com/markqvist/NomadNet/releases/download/$(PKG_VERSION)

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

define Package/nomadnet
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Reticulum
	TITLE:=Nomad Network
	URL:=https://github.com/markqvist/NomadNet
	DEPENDS:= \
		+lxmf \
		+python3-urwid
	USERID:=rns=142:rns=142
endef

define Package/nomadnet-src
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Reticulum
	TITLE:=Nomad Network (Source Code)
	URL:=https://github.com/markqvist/NomadNet
endef

define Package/nomadnet/description
Nomad Network allows you to build private and resilient communications platforms
that are in complete control and ownership of the people that use them.
No signups, no agreements, no handover of any data, no permissions and gatekeepers.

Nomad Network is build on LXMF and Reticulum, which together provides the cryptographic
mesh functionality and peer-to-peer message routing that Nomad Network relies on.
This foundation also makes it possible to use the program over a very wide variety of
communication mediums, from packet radio to fiber optics.

This package contains only bytecode and metadata.
endef

define Package/nomadnet-src/description
Nomad Network allows you to build private and resilient communications platforms
that are in complete control and ownership of the people that use them.
No signups, no agreements, no handover of any data, no permissions and gatekeepers.

Nomad Network is build on LXMF and Reticulum, which together provides the cryptographic
mesh functionality and peer-to-peer message routing that Nomad Network relies on.
This foundation also makes it possible to use the program over a very wide variety of
communication mediums, from packet radio to fiber optics.

This package adds the Python source code.
endef

define Package/nomadnet/conffiles
/etc/config/nomadnet
/var/rns/.nomadnetwork/config
endef

define Package/nomadnet/postinst
#!/bin/sh
[ -n "$$IPKG_INSTROOT" ] || {
	# start service
	/etc/init.d/nomadnet start
}
endef

define Package/nomadnet/prerm
#!/bin/sh
[ -n "$$IPKG_INSTROOT" ] || {
        /etc/init.d/nomadnet disable
        /etc/init.d/nomadnet stop
}
endef

define Py3Package/nomadnet/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/* $(1)/usr/bin/
	# Fix shebangs
	$(SED) '1c#!/usr/bin/python3' $(1)/usr/bin/nomadnet

	# Ensure Python package is installed
	$(INSTALL_DIR) $(1)$(PYTHON3_PKG_DIR)
	$(CP) $(PKG_INSTALL_DIR)$(PYTHON3_PKG_DIR)/* $(1)$(PYTHON3_PKG_DIR)/

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/nomadnet.init $(1)/etc/init.d/nomadnet
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/nomadnet.config $(1)/etc/config/nomadnet

	mkdir -p $(1)/var/rns/.nomadnetwork
	$(INSTALL_DATA) ./files/config $(1)/var/rns/.nomadnetwork/
endef

$(eval $(call Py3Package,nomadnet))
$(eval $(call BuildPackage,nomadnet))
$(eval $(call BuildPackage,nomadnet-src))
