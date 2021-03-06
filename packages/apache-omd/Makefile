include ../../Makefile.omd

NAME = apache-omd
VERSION = 1.0
DIR = $(NAME)-$(VERSION)
FCGI_FILE="mod_fcgid-2.3.6"

MODULE_DIR=$(APACHE_MODULE_DIR)
ifeq ($(shell uname -m),x86_64)
  MODULE_DIR=$(APACHE_MODULE_DIR_64)
endif

APACHE_MODULES=
APACHE_MODULES_INSTALL=
APACHE_FCGID_PATH=$(MODULE_DIR)/$(APACHE_FCGID_MODULE)
CENTOS_WORKAROUND=0
REPLACE_PHP_FCGI_BIN=$(PHP_FCGI_BIN)

ifeq ($(DISTRO_NAME),CENTOS)
   CENTOS_WORKAROUND=1
endif
ifeq ($(DISTRO_NAME),REDHAT)
  CENTOS_WORKAROUND=1
endif

ifeq ($(CENTOS_WORKAROUND), 1)
  CENTOS_WORKAROUND=1
  APACHE_MODULES=modfcgid
  APACHE_MODULES_INSTALL=modfcgid_install
  APACHE_FCGID_PATH=$(OMD_ROOT)/lib/apache/mod_fcgid.so
  # workaround for centos/rh which ignores "php-cgi -d settings"
  REPLACE_PHP_FCGI_BIN=$(PHP_FCGI_BIN) -c $(OMD_ROOT)/etc/apache/php.ini
endif

.PHONY: skel

build: $(APACHE_MODULES)
	# Unpack source code, apply patches, call configure, call make

install: $(APACHE_MODULES_INSTALL)
	# Install software below $(DESTDIR)$(OMD_ROOT)/{bin,lib,share}

skel:
	if [ $(CENTOS_WORKAROUND) -eq 1 ]; then \
		cp /etc/php.ini $(SKEL)/etc/apache/php.ini || exit 1; \
		echo -e "\n\n; OMD OMD OMD OMD OMD OMD\n\nmemory_limit=64M\n\n[Session]\nsession.save_path=###ROOT###/tmp/php/session\nupload_tmp_dir=###ROOT###/tmp/php/upload\nsoap.wsdl_cache_dir=###ROOT###/tmp/php/wsdl-cache\n" >> $(SKEL)/etc/apache/php.ini; \
	fi

	# Replace macros
	sed -i -e 's|###APACHE_MODULE_DIR###|$(MODULE_DIR)|g' \
	       $(SKEL)/etc/apache/apache.conf

	sed -i -e 's|###APACHE_FCGID_MODULE###|$(APACHE_FCGID_PATH)|g' \
	       $(SKEL)/etc/apache/conf.d/02_fcgid.conf

	sed -i 's|###APACHE_BIN###|$(APACHE_BIN)|g' $(SKEL)/etc/init.d/apache
	sed -i 's|###PHP_FCGI_BIN###|$(PHP_FCGI_BIN)|g' $(SKEL)/etc/init.d/apache
	sed -i 's|###PHP_FCGI_BIN###|$(REPLACE_PHP_FCGI_BIN)|g' $(SKEL)/etc/apache/php-wrapper
	# Create working directories
	mkdir -p $(SKEL)/var/log/apache
	mkdir -p $(SKEL)/var/www
	mkdir -p $(SKEL)/tmp/apache/run
	mkdir -p $(SKEL)/tmp/apache/fcgid_sock
	mkdir -p $(SKEL)/tmp/php/session
	mkdir -p $(SKEL)/tmp/php/upload
	mkdir -p $(SKEL)/tmp/php/wsdl-cache

clean:
	# Remove files created by build/install
	rm -rf mod_fcgid-2.3.6

modfcgid:
	tar zxf $(FCGI_FILE).tar.gz
	patch -p0 < ./patches/0001-fcgid_proc_unix.c.patch
	patch -p0 < ./patches/0002-fcgid_pm_unix.c.patch
	cd $(FCGI_FILE) && APXS=/usr/sbin/apxs ./configure.apxs
	$(MAKE) -C $(FCGI_FILE)

modfcgid_install:
	mkdir -p $(DESTDIR)$(OMD_ROOT)/lib/apache
	cp $(FCGI_FILE)/modules/fcgid/.libs/mod_fcgid.so $(DESTDIR)$(OMD_ROOT)/lib/apache/
