DIST ?= any
ARCH ?= amd64

URL_DEB:=$(shell wget --server-response --max-redirect=0 -qO- https://discord.com/api/download\?platform\=linux 2>&1  | sed -n 's|.*\(https://stable.dl2.discordapp.net/apps/linux[^"]*\.deb\)|\1|p')
DEB:=$(shell basename "$(URL_DEB)")

RPM := $(shell echo "$(DEB)" | sed 's/\.deb$$/-2.x86_64.rpm/')

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

out/$(RPM): out/$(DEB)
	@echo converting $(RPM)
	@cd out && fakeroot alien $(DEB) --to-rpm  
	@echo "" >out/rpm.href

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
