include ../../Makefile.omd

.PHONY: skel

NAME = pnp4nagios
VERSION = 0.6.21
DIR = $(NAME)-$(VERSION)
# Unset CONFIG_SITE
CONFIG_SITE = ''

# Configure options for PNP4Nagios. Since we want to compile
# as non-root, we use our own user and group for compiling.
# All files will be packaged as user 'root' later anyway.
CONFIGUREOPTS = \
    --prefix=$(OMD_ROOT) \
    --sysconfdir=$(OMD_ROOT)/etc/pnp4nagios \
    --libexecdir=$(OMD_ROOT)/lib/pnp4nagios \
    --docdir=$(OMD_ROOT)/share/doc/pnp4nagios \
    --datarootdir=$(OMD_ROOT)/share/pnp4nagios/htdocs \
    --localstatedir=$(OMD_ROOT)/var/pnp4nagios \
    --with-perfdata-dir=$(OMD_ROOT)/var/pnp4nagios/perfdata \
    --with-perfdata-spool-dir=$(OMD_ROOT)/tmp/pnp4nagios/spool \
    --with-perfdata-logfile=$(OMD_ROOT)/var/pnp4nagios/log/perfdata.log \
    --with-nagios-user=$$(id -un) \
    --with-nagios-group=$$(id -gn) \
    --with-rrdtool=/bin/true \
    --with-perl_lib_path=$(OMD_ROOT)/lib/perl5/lib/perl5 \
    --with-base-url='/\#\#\#SITE\#\#\#/pnp4nagios'

build:
	tar xzf $(DIR).tar.gz
	cd $(DIR) && rm -rf lib include.orig lib src/lib
	cd $(DIR) && mv include include.orig
	cd $(DIR) && src=`ls -d1 ../../nagios4/nagios/include` && ln -s $$src include
	cd $(DIR) && rm -rf include/lib
	cd $(DIR) && src=`ls -d1 ../../nagios4/nagios/lib` && ln -s $$src .
	cd $(DIR)/src && src=`ls -d1 ../../../nagios4/nagios/lib` && ln -s $$src .
	cd $(DIR)/include && src=`ls -d1 ../../../nagios4/nagios/lib` && ln -s $$src .
	cd $(DIR) && cp include.orig/pnp.h include.orig/npcdmod.h include/
	cd $(DIR) && rm -rf include.orig
	cd $(DIR) ; ./configure $(CONFIGUREOPTS)
	cd $(DIR)/src && sed -e 's/service_check_command/check_command/' -e 's/host_check_command/check_command/' -i npcdmod.c
	$(MAKE) -C $(DIR)/src npcdmod.o

install:
	mkdir -p $(DESTDIR)$(OMD_ROOT)/lib
	cp $(DIR)/src/npcdmod.o $(DESTDIR)$(OMD_ROOT)/lib/npcdmod4.o

skel:
	mkdir -p $(SKEL)/etc/pnp4nagios
	sed -e 's/npcdmod.o/npcdmod4.o/g' $(SKEL)/etc/pnp4nagios/nagios_npcdmod.cfg > $(SKEL)/etc/pnp4nagios/nagios_npcdmod4.cfg

clean:
	rm -rf $(DIR)
