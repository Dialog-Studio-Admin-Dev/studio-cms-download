# Studio CMS instance

You edit only `.env`. Then run: `docker compose up -d`

Show every setting and the state of each secret:
`docker compose exec studio studio config`

## First start

As root (`sudo -i`), in this folder:

```sh
cp .env.example .env
nano .env                 # set STUDIO_DOMAIN
docker login ghcr.io      # your GitHub account with a read:packages token, or the access from the publisher
docker compose up -d
docker compose ps         # repeat until studio shows (healthy)
docker compose exec studio studio admin create ops@agency.example
```

The last command prints a one-time link to set the password of the first
admin account.

## This folder

```text
compose.yaml           official file of this version; do not edit
.env.example           reference for .env
.env                   your settings (copy of .env.example); the only file you edit
compose.override.yaml  optional, your own additions; see examples/
backup-ssh/            optional, SSH key of the backup service; see examples/
examples/              examples for compose.override.yaml, listed in examples/README.md
data/secrets/          secrets generated on first start; part of every backup
data/studio/           databases, project files, media
data/preview/          preview input
data/proxy/            certificates
backups/               local backups and the repository mirror
tools/                 only if installed with install.sh
```

`STUDIO_DATA_DIR` and `STUDIO_BACKUP_DIR` in `.env` move `data/` and
`backups/` to another disk.

## Services

```text
init          runs once before every start: folders, permissions, secrets
studio        editor, API, forms, bookings, statistics
renderer      preview of the websites, isolated from the Studio
preview-gate  the only connection from the Studio to the renderer
delivery      serves pitches (and live websites, if wanted) whose domain points to this server
backup        local backups; off-site copy with restic once RESTIC_REPOSITORY is set
build         builds websites on this server (profile "build")
proxy         HTTPS with Let's Encrypt on ports 80 and 443 (profile "proxy")
maintenance   page shown while the Studio restarts (profile "proxy")
```

Logs: `docker compose logs <service>`. Docker keeps at most 30 MB per service.

## Changes

- Domain or other settings: edit `.env`, then `docker compose up -d`.
- Update: the version comes with `compose.yaml`. Unpack the zip of the new
  version here (`unzip -o studio-compose-<version>.zip`), then
  `docker compose up -d`. The zip replaces only its own files, never `.env`,
  `compose.override.yaml`, `data/` or `backups/`. A line `STUDIO_VERSION`
  in `.env` holds a version; remove it to follow `compose.yaml`.
- Roll back: unpack the zip of the previous version again, then
  `docker compose up -d`.
- Live websites run on a separate web server by default (studio-web zip,
  see the download page). Pitches run on this server, on by default: assign
  the domain to the project in the Studio and point its DNS A record to this
  server; the certificate follows by itself. If wanted, this server serves
  a live website the same way.
- Off-site backup: set `RESTIC_REPOSITORY` and `RESTIC_PASSWORD` in `.env`,
  then `docker compose up -d`. The target must only accept new data
  (append-only); for a target that allows deleting, also set
  `STUDIO_BACKUP_PROTECTION=none`, otherwise the daily deletion check
  reports the backup as failed. An rsync or proof target over SSH:
  `examples/backup-ssh-key.yaml` (key and target).
- Additions from `examples/` (each one listed in `examples/README.md`):
  copy the matching file to `compose.override.yaml`, then
  `docker compose up -d`.
- Advanced settings (handbook, "Advanced settings"): copy
  `examples/advanced-settings.yaml` to `compose.override.yaml`, set the
  value in `.env`, then `docker compose up -d`. Without that file they have
  no effect.
- Recovery kit (secrets and off-site access, keep it offline):
  `docker compose exec backup studio recovery-kit`
- Server lost: new folder from the same zip, `.env` back, backup into
  `backups/`, then before the first start
  `docker compose run --rm --no-deps --user 0 backup studio restore`
  (handbook, "Server lost").

Handbook and downloads: https://github.com/dialog-studio-admin-dev/studio-cms-download
