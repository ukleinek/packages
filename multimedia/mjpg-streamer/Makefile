#
# Copyright (C) 2006-2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=mjpg-streamer
PKG_REV:=181
PKG_VERSION:=r$(PKG_REV)
PKG_RELEASE:=2
PKG_MAINTAINER:=Roger D <rogerdammit@gmail.com>

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https://svn.code.sf.net/p/mjpg-streamer/code/mjpg-streamer
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE_VERSION:=$(PKG_REV)
PKG_SOURCE_PROTO:=svn
PKG_MD5SUM:=b2bc22665733319e647ace236e283684

PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILE:=LICENSE

include $(INCLUDE_DIR)/package.mk

define Package/mjpg-streamer
  SECTION:=multimedia
  CATEGORY:=Multimedia
  TITLE:=MJPG-streamer
  DEPENDS:=+libpthread +libjpeg +PACKAGE_libv4l:libv4l
  URL:=http://mjpg-streamer.wiki.sourceforge.net/
  MENU:=1
endef

define Package/mjpg-streamer/description
 Streaming application for Linux-UVC compatible webcams
endef

define Package/mjpg-streamer/config
  source "$(SOURCE)/Config.in"
endef

EXTRA_CFLAGS += $(TARGET_CPPFLAGS) $(TARGET_LDFLAGS)

define Package/mjpg-streamer/conffiles
/etc/config/mjpg-streamer
endef

CAMBOZOLA:=cambozola-latest.tar.gz

define Download/cambozola
  URL:=http://www.andywilcock.com/code/cambozola
  FILE:=$(CAMBOZOLA)
  MD5SUM:=6c48fd994685d4d72668850eeb613e24
endef

# Fetch latest cambozola that works with latest Java(s)
# Yes, I know this is ugly
define Build/Prepare
    $(call Build/Prepare/Default)
    ifeq ($(CONFIG_MJPG_STREAMER_WWW),y)
	$(eval $(call Download,cambozola))
	$(TAR) -xvf $(DL_DIR)/$(CAMBOZOLA) --strip=2 --wildcards \
		-C $(PKG_BUILD_DIR)/www */dist/cambozola.jar
    endif
endef

ifeq ($(CONFIG_MJPG_STREAMER_V4L2),y)
    TARGET_CFLAGS+= -DUSE_LIBV4L2
    TARGET_LDFLAGS+= -lv4l2
endif

define Package/mjpg-streamer/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/mjpg_streamer $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/etc/config
	$(CP) ./files/mjpg-streamer.config $(1)/etc/config/mjpg-streamer
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/mjpg-streamer.init $(1)/etc/init.d/mjpg-streamer
	$(INSTALL_DIR) $(1)/etc/hotplug.d/usb
	$(INSTALL_DATA) ./files/mjpg-streamer.hotplug $(1)/etc/hotplug.d/usb/20-mjpg-streamer
	$(INSTALL_DIR) $(1)/usr/lib
ifeq ($(CONFIG_MJPG_STREAMER_INPUT_FILE),y)
	$(CP) $(PKG_BUILD_DIR)/input_file.so $(1)/usr/lib
endif
ifeq ($(CONFIG_MJPG_STREAMER_INPUT_UVC),y)
	$(CP) $(PKG_BUILD_DIR)/input_uvc.so $(1)/usr/lib
endif
ifeq ($(CONFIG_MJPG_STREAMER_INPUT_TESTPICTURE),y)
	$(CP) $(PKG_BUILD_DIR)/input_testpicture.so $(1)/usr/lib
endif
ifeq ($(CONFIG_MJPG_STREAMER_OUTPUT_FILE),y)
	$(CP) $(PKG_BUILD_DIR)/output_file.so $(1)/usr/lib
endif
ifeq ($(CONFIG_MJPG_STREAMER_OUTPUT_HTTP),y)
	$(CP) $(PKG_BUILD_DIR)/output_http.so $(1)/usr/lib
	$(INSTALL_DIR) $(1)/www/webcam
    ifeq ($(CONFIG_MJPG_STREAMER_WWW),y)
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/www/* $(1)/www/webcam
    else
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/www/stream_simple.html $(1)/www/webcam/index.html
    endif
endif
endef

$(eval $(call BuildPackage,mjpg-streamer))
