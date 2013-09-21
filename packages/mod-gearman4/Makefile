include ../../Makefile.omd

NAME     = mod_gearman
VERSION  = 1.4.10-nagios4
GEARMAND = $(shell grep "^VERSION " ../gearmand/Makefile | awk '{ print $$3 }')
DIR      = mod_gearman-$(VERSION)

.PHONY: skel

build:
	tar xzf mod_gearman-$(VERSION).tar.gz
	cd $(DIR) && \
	./configure --prefix=$(OMD_ROOT) --with-gearman=$(shell pwd)/../gearmand/gearmand-${GEARMAND} && \
	$(MAKE) -j 1

install:
	mkdir -p $(DESTDIR)$(OMD_ROOT)/lib/mod_gearman
	cp $(DIR)/mod_gearman.o $(DESTDIR)$(OMD_ROOT)/lib/mod_gearman/mod_gearman4.o

skel:
	mkdir -p $(SKEL)/etc/mod-gearman/
	sed -e 's/mod_gearman.o/mod_gearman4.o/g' $(SKEL)/etc/mod-gearman/nagios.cfg > $(SKEL)/etc/mod-gearman/nagios4.cfg

clean:
	rm -rf $(DIR)
