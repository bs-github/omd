include ../../Makefile.omd

NAME = mk-livestatus
VERSION = 1.2.0p3
DIR = $(NAME)-$(VERSION)

CONFIGUREOPTS = \
    --prefix=$(OMD_ROOT)

build:
	test -f livestatus.tar.gz || wget https://github.com/sni/livestatus/tarball/master -O livestatus.tar.gz
	tar xzf livestatus.tar.gz
	rm -f $(DIR)
	ln -s $$(ls -d1 sni-livestatus-*) $(DIR)
	#cd $(DIR) && autoreconf -i
	cd $(DIR) ; ./configure $(CONFIGUREOPTS)
	rm -f $(DIR)/src/lib $(DIR)/src/nagios
	cd $(DIR)/src && src=`ls -d1 ../../../nagios4/nagios/include` && ln -s $$src nagios
	cd $(DIR)/src && src=`ls -d1 ../../../nagios4/nagios/lib` && ln -s $$src .
	$(MAKE) -C $(DIR) all

install:
	mkdir -p $(DESTDIR)$(OMD_ROOT)/lib/mk-livestatus
	cp $(DIR)/src/livestatus.o $(DESTDIR)$(OMD_ROOT)/lib/mk-livestatus/livestatus4.o

skel:

clean:
	rm -rf mk-livestatus-*.*.*[0-9] sni-livestatus-*

upstream:
	rm -f livestatus.tar.gz
	wget https://github.com/sni/livestatus/tarball/master -O livestatus.tar.gz
	tar zxvf livestatus.tar.gz
	src=$$(ls -d1 sni-livestatus-*) && cd $$src && autoreconf -i && cd .. && tar cvfz livestatus.tar.gz $$src
	git add livestatus.tar.gz
