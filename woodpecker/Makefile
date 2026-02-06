DIST ?= any
ARCH ?= amd64

# Recover download URLs from GitHub release assets (latest release, amd64/x86_64)
# https://github.com/woodpecker-ci/woodpecker/releases
RELEASE_URLS:=$(shell wget -qO- https://api.github.com/repos/woodpecker-ci/woodpecker/releases 2>/dev/null | jq -r '.[0].assets[].browser_download_url' 2>/dev/null | grep -E '\.(rpm|deb)$$' | grep -E 'amd64|x86_64' | tr -d '\r' | tr '\n' ' ')

# Extract each URL in shell (avoids Make filter/word-split issues)
URL_AGENT_DEB:=$(shell echo '$(RELEASE_URLS)' | tr ' ' '\n' | grep 'woodpecker-agent_.*\.deb$$' | head -1)
URL_AGENT_RPM:=$(shell echo '$(RELEASE_URLS)' | tr ' ' '\n' | grep 'woodpecker-agent-.*\.rpm$$' | head -1)
URL_CLI_DEB:=$(shell echo '$(RELEASE_URLS)' | tr ' ' '\n' | grep 'woodpecker-cli_.*\.deb$$' | head -1)
URL_CLI_RPM:=$(shell echo '$(RELEASE_URLS)' | tr ' ' '\n' | grep 'woodpecker-cli-.*\.rpm$$' | head -1)
URL_SERVER_DEB:=$(shell echo '$(RELEASE_URLS)' | tr ' ' '\n' | grep 'woodpecker-server_.*\.deb$$' | head -1)
URL_SERVER_RPM:=$(shell echo '$(RELEASE_URLS)' | tr ' ' '\n' | grep 'woodpecker-server-.*\.rpm$$' | head -1)

# Agent
AGENT_DEB:=$(shell basename "$(URL_AGENT_DEB)")
AGENT_RPM:=$(shell basename "$(URL_AGENT_RPM)")

# CLI
CLI_DEB:=$(shell basename "$(URL_CLI_DEB)")
CLI_RPM:=$(shell basename "$(URL_CLI_RPM)")

# Server
SERVER_DEB:=$(shell basename "$(URL_SERVER_DEB)")
SERVER_RPM:=$(shell basename "$(URL_SERVER_RPM)")

# Fail early if release URLs could not be fetched or a package URL is missing
.PHONY: check-urls
check-urls:
	@if [ -z "$(RELEASE_URLS)" ]; then echo "ERROR: Could not fetch release URLs. Check network and that wget/jq are available."; exit 1; fi
	@missing=""; \
	[ -z "$(URL_AGENT_DEB)" ] && missing="$$missing agent-deb"; \
	[ -z "$(URL_AGENT_RPM)" ] && missing="$$missing agent-rpm"; \
	[ -z "$(URL_CLI_DEB)" ] && missing="$$missing cli-deb"; \
	[ -z "$(URL_CLI_RPM)" ] && missing="$$missing cli-rpm"; \
	[ -z "$(URL_SERVER_DEB)" ] && missing="$$missing server-deb"; \
	[ -z "$(URL_SERVER_RPM)" ] && missing="$$missing server-rpm"; \
	if [ -n "$$missing" ]; then echo "ERROR: Could not find package URL(s) in release:$$missing"; exit 1; fi

# --- Agent ---
ifneq ($(URL_AGENT_DEB),)
out/$(AGENT_DEB):
	@mkdir -p out/
	@echo recovering $(AGENT_DEB)
	@wget --show-progress $(URL_AGENT_DEB) -O out/$(AGENT_DEB)
	@echo "$(URL_AGENT_DEB) `sha512sum out/$(AGENT_DEB) | cut -d' ' -f1`" >out/agent-deb.href

../out/deb.$(DIST).$(ARCH)/raw/$(AGENT_DEB): out/$(AGENT_DEB)
	@mkdir -p ../out/deb.$(DIST).$(ARCH)/raw
	@cp out/$(AGENT_DEB) ../out/deb.$(DIST).$(ARCH)/raw/$(AGENT_DEB)
endif

ifneq ($(URL_AGENT_RPM),)
out/$(AGENT_RPM):
	@mkdir -p out/
	@echo recovering $(AGENT_RPM)
	@wget --show-progress $(URL_AGENT_RPM) -O out/$(AGENT_RPM)
	@echo "$(URL_AGENT_RPM) `sha512sum out/$(AGENT_RPM) | cut -d' ' -f1`" >out/agent-rpm.href

../out/rpm.$(DIST).$(ARCH)/raw/$(AGENT_RPM): out/$(AGENT_RPM)
	@mkdir -p ../out/rpm.$(DIST).$(ARCH)/raw
	@cp out/$(AGENT_RPM) ../out/rpm.$(DIST).$(ARCH)/raw/$(AGENT_RPM)
endif

.PHONY: deb_agent_chroot rpm_agent_chroot
ifneq ($(URL_AGENT_DEB),)
deb_agent_chroot: check-urls ../out/deb.$(DIST).$(ARCH)/raw/$(AGENT_DEB)
else
deb_agent_chroot: check-urls
	@echo "ERROR: No URL for agent deb."; exit 1
endif
ifneq ($(URL_AGENT_RPM),)
rpm_agent_chroot: check-urls ../out/rpm.$(DIST).$(ARCH)/raw/$(AGENT_RPM)
else
rpm_agent_chroot: check-urls
	@echo "ERROR: No URL for agent rpm."; exit 1
endif

# --- CLI ---
ifneq ($(URL_CLI_DEB),)
out/$(CLI_DEB):
	@mkdir -p out/
	@echo recovering $(CLI_DEB)
	@wget --show-progress $(URL_CLI_DEB) -O out/$(CLI_DEB)
	@echo "$(URL_CLI_DEB) `sha512sum out/$(CLI_DEB) | cut -d' ' -f1`" >out/cli-deb.href

../out/deb.$(DIST).$(ARCH)/raw/$(CLI_DEB): out/$(CLI_DEB)
	@mkdir -p ../out/deb.$(DIST).$(ARCH)/raw
	@cp out/$(CLI_DEB) ../out/deb.$(DIST).$(ARCH)/raw/$(CLI_DEB)
endif

ifneq ($(URL_CLI_RPM),)
out/$(CLI_RPM):
	@mkdir -p out/
	@echo recovering $(CLI_RPM)
	@wget --show-progress $(URL_CLI_RPM) -O out/$(CLI_RPM)
	@echo "$(URL_CLI_RPM) `sha512sum out/$(CLI_RPM) | cut -d' ' -f1`" >out/cli-rpm.href

../out/rpm.$(DIST).$(ARCH)/raw/$(CLI_RPM): out/$(CLI_RPM)
	@mkdir -p ../out/rpm.$(DIST).$(ARCH)/raw
	@cp out/$(CLI_RPM) ../out/rpm.$(DIST).$(ARCH)/raw/$(CLI_RPM)
endif

.PHONY: deb_cli_chroot rpm_cli_chroot
ifneq ($(URL_CLI_DEB),)
deb_cli_chroot: check-urls ../out/deb.$(DIST).$(ARCH)/raw/$(CLI_DEB)
else
deb_cli_chroot: check-urls
	@echo "ERROR: No URL for cli deb."; exit 1
endif
ifneq ($(URL_CLI_RPM),)
rpm_cli_chroot: check-urls ../out/rpm.$(DIST).$(ARCH)/raw/$(CLI_RPM)
else
rpm_cli_chroot: check-urls
	@echo "ERROR: No URL for cli rpm."; exit 1
endif

# --- Server ---
ifneq ($(URL_SERVER_DEB),)
out/$(SERVER_DEB):
	@mkdir -p out/
	@echo recovering $(SERVER_DEB)
	@wget --show-progress $(URL_SERVER_DEB) -O out/$(SERVER_DEB)
	@echo "$(URL_SERVER_DEB) `sha512sum out/$(SERVER_DEB) | cut -d' ' -f1`" >out/server-deb.href

../out/deb.$(DIST).$(ARCH)/raw/$(SERVER_DEB): out/$(SERVER_DEB)
	@mkdir -p ../out/deb.$(DIST).$(ARCH)/raw
	@cp out/$(SERVER_DEB) ../out/deb.$(DIST).$(ARCH)/raw/$(SERVER_DEB)
endif

ifneq ($(URL_SERVER_RPM),)
out/$(SERVER_RPM):
	@mkdir -p out/
	@echo recovering $(SERVER_RPM)
	@wget --show-progress $(URL_SERVER_RPM) -O out/$(SERVER_RPM)
	@echo "$(URL_SERVER_RPM) `sha512sum out/$(SERVER_RPM) | cut -d' ' -f1`" >out/server-rpm.href

../out/rpm.$(DIST).$(ARCH)/raw/$(SERVER_RPM): out/$(SERVER_RPM)
	@mkdir -p ../out/rpm.$(DIST).$(ARCH)/raw
	@cp out/$(SERVER_RPM) ../out/rpm.$(DIST).$(ARCH)/raw/$(SERVER_RPM)
endif

.PHONY: deb_server_chroot rpm_server_chroot
ifneq ($(URL_SERVER_DEB),)
deb_server_chroot: check-urls ../out/deb.$(DIST).$(ARCH)/raw/$(SERVER_DEB)
else
deb_server_chroot: check-urls
	@echo "ERROR: No URL for server deb."; exit 1
endif
ifneq ($(URL_SERVER_RPM),)
rpm_server_chroot: check-urls ../out/rpm.$(DIST).$(ARCH)/raw/$(SERVER_RPM)
else
rpm_server_chroot: check-urls
	@echo "ERROR: No URL for server rpm."; exit 1
endif

# Combined chroot targets (all packages)
.PHONY: deb_chroot rpm_chroot
deb_chroot: deb_agent_chroot deb_cli_chroot deb_server_chroot
rpm_chroot: rpm_agent_chroot rpm_cli_chroot rpm_server_chroot

.PHONY: clean
clean:
	@rm -rf out
	@rm -rf ../out/deb.*.*/raw/$(AGENT_DEB)
	@rm -rf ../out/deb.*.*/raw/$(CLI_DEB)
	@rm -rf ../out/deb.*.*/raw/$(SERVER_DEB)
	@rm -rf ../out/rpm.*.*/raw/$(AGENT_RPM)
	@rm -rf ../out/rpm.*.*/raw/$(CLI_RPM)
	@rm -rf ../out/rpm.*.*/raw/$(SERVER_RPM)
