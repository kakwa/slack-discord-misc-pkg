DIST ?= any
ARCH ?= amd64

# Recover download URLs from Discord's installer redirector (stable channel, x64)
# https://discord.com/api/downloads/distributions/app/installers/latest?channel=stable&platform=linux&arch=x64&format=deb
URL_DEB:=$(shell wget --server-response --max-redirect=0 -qO- "https://discord.com/api/downloads/distributions/app/installers/latest?channel=stable&platform=linux&arch=x64&format=deb" 2>&1 | grep -iE '^\s*location:' | awk '{print $$NF}' | tail -1)
# Upstream version is the second-to-last path segment of the redirect URL (…/x64/1.0.168/Discord.deb)
VERSION:=$(shell echo "$(URL_DEB)" | awk -F/ '{print $$(NF-1)}')
# Debian-style package name: discord_<version>_<arch>
DEB:=discord_$(VERSION)_$(ARCH).deb

URL_RPM:=$(shell wget --server-response --max-redirect=0 -qO- "https://discord.com/api/downloads/distributions/app/installers/latest?channel=stable&platform=linux&arch=x64&format=rpm" 2>&1 | grep -iE '^\s*location:' | awk '{print $$NF}' | tail -1)
# RPM-style package name: discord_<version>.x86_64 (Discord builds the rpm for x86_64)
RPM:=discord_$(VERSION).x86_64.rpm

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
