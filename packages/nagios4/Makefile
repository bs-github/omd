include ../../Makefile.omd

NAME = nagios
VERSION = 4.0.0
DIR = $(NAME)

# Configure options for Nagios. Since we want to compile
# as non-root, we use our own user and group for compiling.
# All files will be packaged as user 'root' later anyway.
CONFIGUREOPTS = \
    --sbindir=$(OMD_ROOT)/lib/nagios/cgi-bin \
    --bindir=$(OMD_ROOT)/bin \
    --datarootdir=$(OMD_ROOT)/share/nagios/htdocs \
    --with-nagios-user=$$(id -un) \
    --with-nagios-group=$$(id -gn)

build:
	tar xzf nagios-$(VERSION).tar.gz
	set -e ; for p in patches/*.dif ; do \
	    echo "applying $$p..." ; \
	    ( cd $(DIR) ; patch -p1 -b ) < $$p ; \
	done
	cd $(DIR) ; ./configure $(CONFIGUREOPTS)
	$(MAKE) -C $(DIR) all

install:
	mkdir -p $(DESTDIR)$(OMD_ROOT)/bin
	cp $(DIR)/base/nagios $(DESTDIR)$(OMD_ROOT)/bin/nagios4
	chmod 755 $(DESTDIR)$(OMD_ROOT)/bin/nagios4

skel:

clean:
	rm -rf $(DIR)

upstream:
	rm -f nagios-master.tar.gz
	wget https://github.com/ageric/nagios/tarball/master -O nagios-master.tar.gz
	git add nagios-master.tar.gz
