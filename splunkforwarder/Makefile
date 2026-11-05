DIST ?= any
ARCH ?= amd64

# Recover download URLs from Splunk's universal forwarder download page (linux amd64/x86_64)
# https://www.splunk.com/en_us/download/universal-forwarder.html
# The page rejects the default wget user agent, so a browser-like one is required
URL_DEB:=$(shell wget -qO- --user-agent="Mozilla/5.0" https://www.splunk.com/en_us/download/universal-forwarder.html | grep -oE 'https://download\.splunk\.com/products/universalforwarder/releases/[^" <]*-linux-amd64\.deb' | tail -1)
DEB:=$(shell basename "$(URL_DEB)")

URL_RPM:=$(shell wget -qO- --user-agent="Mozilla/5.0" https://www.splunk.com/en_us/download/universal-forwarder.html | grep -oE 'https://download\.splunk\.com/products/universalforwarder/releases/[^" <]*\.x86_64\.rpm' | tail -1)
RPM:=$(shell basename "$(URL_RPM)")

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

../out/rpm.$(DIST).$(ARCH)/raw/$(RPM): out/$(RPM)
	@mkdir -p ../out/rpm.$(DIST).$(ARCH)/raw
	@cp out/$(RPM) ../out/rpm.$(DIST).$(ARCH)/raw/$(RPM)

.PHONY: rpm_chroot
rpm_chroot: ../out/rpm.$(DIST).$(ARCH)/raw/$(RPM)

.PHONY: clean
clean:
	@rm -rf out
	@rm -rf ../out/deb.*.*/raw/$(DEB)
	@rm -rf ../out/rpm.*.*/raw/$(RPM)
