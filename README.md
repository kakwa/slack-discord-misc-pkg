[![Build Packages Repositories](https://github.com/kakwa/slack-discord-misc-pkg/actions/workflows/repos.yml/badge.svg)](https://github.com/kakwa/slack-discord-misc-pkg/actions/workflows/repos.yml)

# slack-discord-misc-pkg

The `.deb`/`.rpm` repositories are available at the following url: https://kakwa.github.io/slack-discord-misc-pkg/

## Ubuntu/Debian

If you are using `Ubuntu`/`Debian`, here how to install the repository:

```shell
# If you are not root
export SUDO=sudo

# Get your OS version
. /etc/os-release
# Get the architecture
ARCH=$(dpkg --print-architecture)

# Add the GPG key
wget -qO - https://kakwa.github.io/slack-discord-misc-pkg/GPG-KEY.pub | \
    gpg --dearmor | ${SUDO} tee /etc/apt/trusted.gpg.d/slack-discord-misc-pkg.gpg >/dev/null

# Add the repository
echo "deb [arch=${ARCH}] \
https://kakwa.github.io/slack-discord-misc-pkg/deb.${VERSION_CODENAME}.${ARCH}/ \
${VERSION_CODENAME} main" | ${SUDO} tee /etc/apt/sources.list.d/slack-discord-misc-pkg.list

# update
apt update
```

## RHEL/Rocky/Fedora

If you are using `RHEL`/`Rocky`/`Fedora`, here how to install the repository:

```shell
# If you are not root
export SUDO=sudo

# Get your OS version
. /etc/os-release

# Determine distro prefix (el for RHEL/Rocky, fc for Fedora)
if [[ "$ID" == "fedora" ]]; then
    DISTRO_PREFIX="fc"
else
    DISTRO_PREFIX="el"
fi

# Create the repository file
cat << EOF | ${SUDO} tee /etc/yum.repos.d/slack-discord-misc-pkg.repo
[slack-discord-misc-pkg]
name=slack-discord-misc-pkg
baseurl=https://kakwa.github.io/slack-discord-misc-pkg/rpm.${DISTRO_PREFIX}\$releasever.\$basearch/\$releasever/\$basearch/
enabled=1
gpgcheck=1
gpgkey=https://kakwa.github.io/slack-discord-misc-pkg/GPG-KEY.pub
EOF
```

# Build The `.rpm`/`.deb` Repositories

This project uses [Pakste](https://github.com/kakwa/pakste).

Check the [Pakste Documention](https://kakwa.github.io/pakste/) for more details.
