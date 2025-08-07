DIST ?= any
ARCH ?= amd64

VERSION:=$(shell wget -qO- https://api.github.com/repos/openbao/openbao/releases | jq -r '.[0] .tag_name' | sed 's/^v//')
URL=https://github.com/openbao/openbao/releases/download/v$(VERSION)/bao_$(VERSION)_linux_amd64

URL_DEB:=$(URL).deb
DEB:=$(shell basename "$(URL_DEB)")
URL_RPM:=$(URL).rpm
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
