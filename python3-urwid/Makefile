include $(TOPDIR)/rules.mk

PKG_NAME:=python3-urwid
PKG_VERSION:=2.6.16
PKG_RELEASE:=1

PYPI_NAME:=urwid
PKG_HASH:=93ad239939e44c385e64aa00027878b9e5c486d59e855ec8ab5b1e1adcdb32a2

# https://github.com/egorenar/openwrt-packages-custom/tree/master/python3-urwid
PKG_MAINTAINER:=Alexander Egorenkov <egorenar-dev@posteo.net>
PKG_LICENSE:=LGPL-2.1-or-later
PKG_LICENSE_FILES:=COPYING

include $(TOPDIR)/feeds/packages/lang/python/pypi.mk
include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/python/python3-package.mk

define Package/python3-urwid
  SECTION:=lang-python
  CATEGORY:=Languages
  SUBMENU:=Python
  TITLE:=A console user interface library for Python
  URL:=https://github.com/urwid/urwid
  DEPENDS:= \
    +python3-light \
    +python3-typing-extensions \
    +python3-wcwidth
endef

define Package/python3-urwid/description
 Urwid is a console user interface library for Python on Linux, OSX, Cygwin or other unix-like OS and partially supports Windows OS.
endef

$(eval $(call Py3Package,python3-urwid))
$(eval $(call BuildPackage,python3-urwid))
$(eval $(call BuildPackage,python3-urwid-src))
