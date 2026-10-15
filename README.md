# Studio CMS downloads

Everything a self-hosted Studio CMS needs, one release per version. The images
themselves need the download access of the publisher (`docker login ghcr.io`).

Current stable version: **1.1.1**
([files and release notes](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/tag/studio/v1.1.1))

See the files of version 1.1.1 before you download:
[`compose.yaml`](compose/compose.yaml), [`.env.example`](compose/.env.example)
and [`README.md`](compose/README.md). `compose.yaml` and `.env.example` are
in the zip; `.env` is your copy. `.env.example` starts with a dot and is
hidden: `ls -a` shows it, the macOS Finder with Cmd+Shift+.

Handbook (German, one HTML file): [handbook.html](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/handbook.html).
It explains every step below, the web server, every setting and what to do
in an emergency.

## What you need

Studio server:

- Ubuntu 22.04, 24.04 or 26.04 LTS on x86-64, 2 CPUs, 8 GB RAM, at least
  5 GB free disk for data and 5 GB for backups (about 160 GB for 50
  projects with usual images; videos come on top)
- Docker Engine with the Compose plugin 2.24.4 or later
  (`docker compose version`) and `unzip`; the handbook, chapter 2,
  appendix, installs both from Docker's package repository
- the ports 80 and 443 open from outside and not in use, a DNS A record of
  the Studio's address pointing to the server, and root (`sudo -i`)
- for `docker login ghcr.io`: if the publisher gave your GitHub account
  read access, your GitHub user name and a token (classic) with
  `read:packages` from https://github.com/settings/tokens/new?scopes=read:packages
  (fine-grained tokens do not work with ghcr.io); otherwise the user and
  token the publisher sent you

Web server for live websites: Ubuntu with Docker as above, 1 CPU and 1 GB
RAM, the ports 22, 80 and 443 open from outside, and a DNS A record per
website pointing to it.

## Install by hand

```sh
sudo -i                              # every command below runs as root
mkdir -p /opt/studio && cd /opt/studio
curl -fsSLO https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/studio-compose-1.1.1.zip
unzip studio-compose-1.1.1.zip
cp .env.example .env && nano .env    # set STUDIO_DOMAIN
docker login ghcr.io                 # your GitHub account with a read:packages token, or the access from the publisher
docker compose up -d
docker compose ps                    # repeat until studio shows (healthy)
docker compose exec studio studio admin create ops@agency.example
```

`README.md` in the folder explains every file, service and change. Update:
unpack the zip of the new version in the same folder (`unzip -o`), then
`docker compose up -d`; `.env` and the data stay.

## Web server for the websites

Live websites run on a web server of their own by default; the Studio
server serves pitches and, if you want, live websites too. On the web
server:

```sh
sudo -i                              # every command below runs as root
mkdir -p /opt/studio-web && cd /opt/studio-web
curl -fsSLO https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/studio-web-1.1.1.zip
unzip studio-web-1.1.1.zip && docker compose up -d
```

No `docker login` there: both images are public. Then add the server in the
Studio and run the one command it shows; `README.md` in that folder and the
handbook, chapter 3, show the steps.

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
| [`studio-web-1.1.1.zip`](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/studio-web-1.1.1.zip) | web server: `compose.yaml`, `.env.example`, `README.md`, `delivery-service.mjs`, `add-studio.sh` |
| [`handbook.html`](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/handbook.html) | handbook (German) |
| [`studio-install-1.1.1.sh`](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/studio-install-1.1.1.sh) | installer of this version (the command above always takes the current one) |
| [`studio-self-hosting-v1.1.1.tar.gz`](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/studio-self-hosting-v1.1.1.tar.gz) | tools of the one-command installation |
| [`SHA256SUMS`](https://github.com/Dialog-Studio-Admin-Dev/studio-cms-download/releases/download/studio/v1.1.1/SHA256SUMS) | checksums of the files above |

Check a download: `sha256sum --check --ignore-missing SHA256SUMS`
