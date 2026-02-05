DIST ?= any
ARCH ?= amd64

# Recover download URLs from GitHub release assets (latest release, amd64)
RELEASE_URLS:=$(shell wget -qO- https://api.github.com/repos/openbao/openbao/releases | jq '.[0].assets[].browser_download_url' -r | grep -E '\.(rpm|deb)$$' | grep amd64)

# Standard openbao packages
URL_DEB:=$(filter-out %hsm%,$(filter %.deb,$(RELEASE_URLS)))
URL_RPM:=$(filter-out %hsm%,$(filter %.rpm,$(RELEASE_URLS)))
DEB:=$(shell basename "$(URL_DEB)")
RPM:=$(shell basename "$(URL_RPM)")

# HSM variants (openbao-hsm)
URL_DEB_HSM:=$(filter %hsm%,$(filter %.deb,$(RELEASE_URLS)))
URL_RPM_HSM:=$(filter %hsm%,$(filter %.rpm,$(RELEASE_URLS)))
DEB_HSM:=$(shell basename "$(URL_DEB_HSM)")
RPM_HSM:=$(shell basename "$(URL_RPM_HSM)")

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

# HSM package targets
out/$(DEB_HSM):
	@mkdir -p out/
	@echo recovering $(DEB_HSM)
	@wget --show-progress $(URL_DEB_HSM) -O out/$(DEB_HSM)
	@echo "$(URL_DEB_HSM) `sha512sum out/$(DEB_HSM) | cut -d' ' -f1`" >out/deb-hsm.href

out/$(RPM_HSM):
	@mkdir -p out/
	@echo recovering $(RPM_HSM)
	@wget --show-progress $(URL_RPM_HSM) -O out/$(RPM_HSM)
	@echo "$(URL_RPM_HSM) `sha512sum out/$(RPM_HSM) | cut -d' ' -f1`" >out/rpm-hsm.href

../out/deb.$(DIST).$(ARCH)/raw/$(DEB_HSM): out/$(DEB_HSM)
	@mkdir -p ../out/deb.$(DIST).$(ARCH)/raw
	@cp out/$(DEB_HSM) ../out/deb.$(DIST).$(ARCH)/raw/$(DEB_HSM)

../out/rpm.$(DIST).$(ARCH)/raw/$(RPM_HSM): out/$(RPM_HSM)
	@mkdir -p ../out/rpm.$(DIST).$(ARCH)/raw
	@cp out/$(RPM_HSM) ../out/rpm.$(DIST).$(ARCH)/raw/$(RPM_HSM)

.PHONY: deb_hsm_chroot rpm_hsm_chroot
deb_hsm_chroot: ../out/deb.$(DIST).$(ARCH)/raw/$(DEB_HSM)
rpm_hsm_chroot: ../out/rpm.$(DIST).$(ARCH)/raw/$(RPM_HSM)

.PHONY: clean
clean:
	@rm -rf out
	@rm -rf ../out/deb.*.*/raw/$(DEB)
	@rm -rf ../out/deb.*.*/raw/$(DEB_HSM)
	@rm -rf ../out/rpm.*.*/raw/$(RPM)
	@rm -rf ../out/rpm.*.*/raw/$(RPM_HSM)
