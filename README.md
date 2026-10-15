# Studio CMS downloads

Everything a self-hosted Studio CMS needs, one release per version. The images
themselves need the download access of the publisher (`docker login ghcr.io`).

Current stable version: **1.1.1**
([files and release notes](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/tag/studio/v1.1.1))

Handbook (German, one HTML file): [handbook.html](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/handbook.html).
It explains every step below, the web server, every setting and what to do
in an emergency.

You need an Ubuntu server (22.04, 24.04 or 26.04, x86-64, 8 GB RAM) with
Docker, its Compose plugin and `unzip` (the handbook, chapter 2, appendix,
shows how to install them), a DNS A record of the Studio's address pointing
to it, and the ports 80 and 443 open. For `docker login ghcr.io`: if the
publisher gave your GitHub account read access, your GitHub user name and a
token (classic) with `read:packages`; otherwise the user and token the
publisher sent you.

## Install by hand

```sh
mkdir -p /opt/studio && cd /opt/studio
curl -fsSLO https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/studio-compose-1.1.1.zip
unzip studio-compose-1.1.1.zip
cp .env.example .env && nano .env    # set STUDIO_DOMAIN
docker login ghcr.io                 # your GitHub account with a read:packages token, or the access from the publisher
docker compose up -d
docker compose exec studio studio admin create ops@agency.example
```

`README.md` in the folder explains every file, service and change.

## Web server for the websites

Live websites usually run on a server of their own. On that server:

```sh
mkdir -p /opt/studio-web && cd /opt/studio-web
curl -fsSLO https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/studio-web-1.1.1.zip
unzip studio-web-1.1.1.zip && cp .env.example .env
docker login ghcr.io && docker compose up -d
```

`README.md` in that folder names the account for the Studio and where its key
goes; the handbook, chapter 3, shows the steps in the Studio.

## Install with one command

```sh
curl -fsSL https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/latest/download/install.sh | sudo bash
```

This loads `install.sh` of the latest release; `studio-install-<version>.sh`
below is the same installer for one version.

## Files of each release

| File | Content |
|---|---|
| [`studio-compose-1.1.1.zip`](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/studio-compose-1.1.1.zip) | `compose.yaml`, `.env.example`, `README.md`, `examples/` |
| [`studio-web-1.1.1.zip`](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/studio-web-1.1.1.zip) | web server: `compose.yaml`, `.env.example`, `README.md` |
| [`handbook.html`](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/handbook.html) | handbook (German) |
| [`studio-install-1.1.1.sh`](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/studio-install-1.1.1.sh) | installer of this version (the command above always takes the current one) |
| [`studio-self-hosting-v1.1.1.tar.gz`](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/studio-self-hosting-v1.1.1.tar.gz) | tools of the one-command installation |
| [`SHA256SUMS`](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/SHA256SUMS) | checksums of the files above |

Check a download: `sha256sum --check --ignore-missing SHA256SUMS`
