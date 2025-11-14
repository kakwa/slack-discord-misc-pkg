[![Build Packages Repositories](https://github.com/kakwa/slack-discord-misc-pkg/actions/workflows/repos.yml/badge.svg)](https://github.com/kakwa/slack-discord-misc-pkg/actions/workflows/repos.yml)

# slack-discord-misc-pkg

`rpm`/`deb` repositories for slack, discord and other upstream packages.

This repo is automatically updated daily.

Url: https://kakwa.github.io/slack-discord-misc-pkg/

# Notes

* `discord` `.rpm` is a basic conversion from the `.deb` using `alien`.

## Ubuntu/Debian

If you are using `Ubuntu`/`Debian`, here how to install the repository:

```shell
# If you are not root
export SUDO=sudo

# Get your OS version
# Add the GPG key
curl -s https://kakwa.github.io/slack-discord-misc-pkg/GPG-KEY.pub | \
    ${SUDO} tee /etc/apt/trusted.gpg.d/slack-discord-misc-pkg.asc >/dev/null

# Add the repository
cat << EOF | ${SUDO} tee /etc/apt/sources.list.d/slack-discord-misc-pkg.sources
Types: deb
URIs: https://kakwa.github.io/slack-discord-misc-pkg/deb.stable.amd64/
Suites: stable
Components: main
Architectures: amd64
Signed-By: /etc/apt/trusted.gpg.d/slack-discord-misc-pkg.asc
EOF

# update
apt update
```

## RHEL/Rocky/Fedora

If you are using `RHEL`/`Rocky`/`Fedora`, here how to install the repository:

```shell
# Create the repository file
cat << EOF | ${SUDO} tee -a /etc/dnf/dnf.conf

[slack-discord-misc-pkg]
name=slack-discord-misc-pkg
baseurl=https://kakwa.github.io/slack-discord-misc-pkg/rpm.stable.x86_64/
enabled=1
gpgcheck=1
gpgkey=https://kakwa.github.io/slack-discord-misc-pkg/GPG-KEY.pub
EOF
```
