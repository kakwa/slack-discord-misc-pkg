DIST ?= any
ARCH ?= amd64

ifneq (,$(findstring deb,$(MAKECMDGOALS)))
URL_DEB:=$(shell wget -qO- https://www.splunk.com/en_us/download/universal-forwarder.html | sed -n 's|.*="\(https://download.splunk.com/products/universalforwarder/releases/[^"]*.deb\)".*|\1|p' | grep amd64)
DEB:=$(shell basename "$(URL_DEB)")
else
URL_RPM:=$(shell wget -qO- https://www.splunk.com/en_us/download/universal-forwarder.html | sed -n 's|.*="\(https://download.splunk.com/products/universalforwarder/releases/[^"]*.rpm\)".*|\1|p' | grep x86_64)
RPM:=$(shell basename "$(URL_RPM)")
endif

out/$(DEB):
	@mkdir -p out/
	@echo recovering $(DEB)
	@wget --show-progress $(URL_DEB) -O out/$(DEB)
	@echo "$(URL_DEB) `sha512sum out/$(DEB) | cut -d' ' -f1`" >out/deb.href

../out/deb.$(DIST).$(ARCH)/raw/$(DEB): out/$(DEB)
	@mkdir -p ../out/deb.$(DIST).$(ARCH)/raw
	@cp out/$(DEB) ../out/deb.$(DIST).$(ARCH)/raw/$(DEB)

.PHONY: deb_chroot
deb_chroot: ../out/deb.$(DIST).$(ARCH)/raw/$(DEB)

out/$(RPM):
	@mkdir -p out/
	@echo recovering $(RPM)
	@wget --show-progress $(URL_RPM) -O out/$(RPM)
	@echo "$(URL_RPM) `sha512sum out/$(RPM) | cut -d' ' -f1`" >out/rpm.href

../out/RPM.$(DIST).$(ARCH)/raw/$RPM): out/$(RPM)
	@mkdir -p ../out/rpm.$(DIST).$(ARCH)/raw
	@cp out/$(RPM) ../out/rpm.$(DIST).$(ARCH)/raw/$(RPM)

.PHONY: rpm_chroot
rpm_chroot:  ../out/RPM.$(DIST).$(ARCH)/raw/$RPM)

.PHONY: clean
clean:
	@rm -rf out
	@rm -rf ../out/deb.*.*/raw/$(DEB)
	@rm -rf ../out/rpm.*.*/raw/$(RPM)
