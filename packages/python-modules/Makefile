include ../../Makefile.omd

NAME     = python-modules
VERSION  = $(OMD_VERSION)
DISTRO   = $(shell ../../distro)

MODULES = pysphere-0.1.7.zip \
          pyasn1-0.1.7.tar.gz \
          pysnmp-4.2.4.tar.gz

build:
	mkdir -p dest
	cd dest && \
	    for M in $(MODULES); do \
		if echo $$M | grep .tar.gz; then \
		    tar xvzf ../src/$$M ; \
		else \
		    unzip -o ../src/$$M ; \
		fi \
	    done

install:
	mkdir -p $(DESTDIR)$(OMD_ROOT)/lib/python
	pwd
	cd dest && \
	    export PYTHONPATH=$$PYTHONPATH:$(DESTDIR)$(OMD_ROOT)/lib/python ; \
	    for M in $(shell ls dest); do \
		cd $$M ; \
		python setup.py install --home=$(DESTDIR)$(OMD_ROOT) --prefix='' ; \
		if [ -n "$(DESTDIR)$(OMD_ROOT)" -a -d "$(DESTDIR)$(OMD_ROOT)/lib64" ] ; then rsync -avh $(DESTDIR)$(OMD_ROOT)/lib64/* $(DESTDIR)$(OMD_ROOT)/lib/ ; rm -rf $(DESTDIR)$(OMD_ROOT)/lib64 ; fi ;\
		cd .. ; \
	    done

skel:

clean:
	rm -rf dest
