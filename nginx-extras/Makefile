#
# Copyright (C) 2011-2018 Entware
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# Extract debian/modules from http://ftp.debian.org/debian/pool/main/n/nginx/nginx_1.13.12-1.debian.tar.xz
# to src folder.
# Manually added hiredis/* to modules/nchan
# Debian differences: threads, geoip, http_upstream_zone, stream_upstream_zone
# http_perl modules are turned off

include $(TOPDIR)/rules.mk

PKG_NAME:=nginx-extras
PKG_VERSION:=1.14.0
PKG_RELEASE:=1

PKG_SOURCE:=nginx-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://nginx.org/download
PKG_HASH:=5d15becbf69aba1fe33f8d416d97edd95ea8919ea9ac519eff9bafebb6022cb5

PKG_BUILD_DIR:=$(BUILD_DIR)/nginx-$(PKG_VERSION)

PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk

define Package/nginx-extras
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=Web Servers/Proxies
	TITLE:=Nginx web server
	URL:=http://nginx.org/
	DEPENDS:=+libpam +libexpat +libopenssl +libexslt +libgd +liblua +libpcre
	CONFLICTS:=nginx
endef

define Package/nginx-extras/description
 nginx is an HTTP and reverse proxy server, as well as a mail proxy server,
 written by Igor Sysoev. This is mainline version where all available
 plugins and patches are taken from Debian nginx-extras package.
endef

define Build/Patch
	$(call Build/Patch/Default)
	( cd $(PKG_BUILD_DIR); \
	    for series_file in `find modules/patches -name series`; do \
		mod=`echo $$$$series_file | cut -d'/' -f3`; \
		for mod_patch in `cat $$$$series_file`; do \
		patch -d modules/$$$$mod -p1 -i $(PKG_BUILD_DIR)/modules/patches/$$$$mod/$$$$mod_patch; \
	    done \
	done \
	)
endef

define Package/nginx-extras-mod-luci/default
  TITLE:=Nginx-extras on LuCI
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  TITLE:=Support file for Nginx
  URL:=http://nginx.org/
  DEPENDS:=+uwsgi-cgi +uwsgi-cgi-luci-support
	CONFLICTS:=nginx-mod-luci
endef

define Package/nginx-extras-mod-luci
  $(Package/nginx-mod-luci/default)
  DEPENDS += +nginx-extras
	CONFLICTS:=nginx-mod-luci
endef

define Package/nginx-extras-mod-luci/description
 Support file for LuCI in nginx. Include custom nginx configuration, autostart script for uwsgi.
endef

define Package/nginx-extras-mod-luci-ssl
  $(Package/nginx-extras-mod-luci/default)
  TITLE += with HTTPS support
  DEPENDS += +nginx-extras
	CONFLICTS:=nginx-mod-luci-ssl
endef

Package/nginx-mod-luci-ssl/description = $(define Package/nginx-mod-luci/description) \
  This also include redirect from http to https and cert autogeneration.

TARGET_CFLAGS += \
	-I$(STAGING_DIR)/usr/include/libxml2 -Wno-error=parentheses

#ifeq ($(CONFIG_GCC_USE_VERSION_7_3_0),y)
TARGET_CFLAGS += -Wno-error=implicit-fallthrough
#endif

TARGET_LDFLAGS += \
	-lxml2

define Build/Configure
	( cd $(PKG_BUILD_DIR); \
		./configure \
			--crossbuild=Linux::$(ARCH) \
			--with-cc="$(TARGET_CC)" \
			--with-cc-opt="$(TARGET_CPPFLAGS) $(TARGET_CFLAGS)" \
			--with-ld-opt="$(TARGET_LDFLAGS)" \
			--prefix=/usr \
			--conf-path=/etc/nginx/nginx.conf \
			--http-log-path=/var/log/nginx/access.log \
			--error-log-path=/var/log/nginx/error.log \
			--lock-path=/var/lock/nginx.lock \
			--pid-path=/var/run/nginx.pid \
			--modules-path=/usr/lib/nginx \
			--http-client-body-temp-path=/var/lib/nginx/body \
			--http-fastcgi-temp-path=/var/lib/nginx/fastcgi \
			--http-proxy-temp-path=/var/lib/nginx/proxy \
			--http-scgi-temp-path=/var/lib/nginx/scgi \
			--http-uwsgi-temp-path=/var/lib/nginx/uwsgi \
			--with-debug \
			--with-pcre-jit \
			--with-http_ssl_module \
			--with-http_stub_status_module \
			--with-http_realip_module \
			--with-http_auth_request_module \
			--with-http_v2_module \
			--with-http_dav_module \
			--with-http_slice_module \
			\
			--without-http_upstream_zone_module \
			--without-stream_upstream_zone_module \
			\
			--with-http_addition_module \
			--with-http_flv_module \
			--with-http_gunzip_module \
			--with-http_gzip_static_module \
			--with-http_image_filter_module=dynamic \
			--with-http_mp4_module \
			--with-http_random_index_module \
			--with-http_secure_link_module \
			--with-http_sub_module \
			--with-http_xslt_module=dynamic \
			--with-mail=dynamic \
			--with-mail_ssl_module \
			--with-stream=dynamic \
			--with-stream_ssl_module \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-headers-more-filter \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-auth-pam \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-cache-purge \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-dav-ext \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-ndk \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-echo \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-fancyindex \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/nchan \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-lua \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/rtmp \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-uploadprogress \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-upstream-fair \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-subs-filter \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-brotli \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/http-ts \
			--add-dynamic-module=$(PKG_BUILD_DIR)/modules/naxsi/naxsi_src \
	)
endef

define Package/nginx-extras/conffiles
/etc/nginx/
endef

define Package/nginx-extras-mod-luci/install
	$(INSTALL_DIR) $(1)/etc/nginx
	$(INSTALL_BIN) ./files-luci-support/luci_uwsgi.conf $(1)/etc/nginx/luci_uwsgi.conf
	$(INSTALL_BIN) ./files-luci-support/luci_nginx.conf $(1)/etc/nginx/luci_nginx.conf
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) ./files-luci-support/60_nginx-luci-support $(1)/etc/uci-defaults/60_nginx-luci-support
endef

define Package/nginx-extras-mod-luci-ssl/install
	$(Package/nginx-mod-luci/install)
	$(INSTALL_DIR) $(1)/etc/nginx
	$(INSTALL_BIN) ./files-luci-support/luci_nginx_ssl.conf $(1)/etc/nginx/luci_nginx_ssl.conf
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) ./files-luci-support/70_nginx-luci-support-ssl $(1)/etc/uci-defaults/70_nginx-luci-support-ssl
endef

define Package/nginx-extras/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/nginx $(1)/usr/sbin
	$(INSTALL_DIR) $(1)/usr/lib/nginx
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/lib/nginx/ngx_*.so $(1)/usr/lib/nginx
	$(INSTALL_DIR) $(1)/usr/html
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/html/*.html $(1)/usr/html
	$(INSTALL_DIR) $(1)/etc/nginx/{sites-available,sites-enabled,snippets}
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/etc/nginx/* $(1)/etc/nginx
	$(RM) $(1)/etc/nginx/*.default $(1)/etc/nginx/nginx.conf
	$(INSTALL_CONF) files/default $(1)/etc/nginx/sites-available
	$(INSTALL_CONF) files/{fastcgi-php,snakeoil}.conf $(1)/etc/nginx/snippets
	$(INSTALL_CONF) files/nginx.conf $(1)/etc/nginx
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) files/S80nginx $(1)/etc/init.d
	$(INSTALL_DIR) $(1)/opt/share/www
	$(INSTALL_DIR) $(1)/etc/nginx
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/modules/naxsi/nginx-naxsi/naxsi_config/naxsi_core.rules $(1)/etc/nginx
	chmod 0640 $(1)/etc/nginx/naxsi_core.rules
endef

define Package/nginx-extras/postinst
#!/bin/sh

if [ -e /etc/nginx/sites-enabled/default ]; then
    echo '/etc/nginx/sites-enabled/default exists, not linking default one.'
else
    ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled
    echo '/etc/nginx/sites-enabled/default site enabled.'
fi
exit 0
endef

$(eval $(call BuildPackage,nginx-extras))
