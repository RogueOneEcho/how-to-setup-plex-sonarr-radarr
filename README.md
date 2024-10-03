# Automated downloads with Sonarr, Radarr, Lidarr and Readarr

This is part 1 of the series: [how to setup Plex, Sonarr, Radarr, etc](https://github.com/RogueOneEcho/how-to-setup-plex-sonarr-radarr).

This part adds automated downloads and media management of TV shows, movies, music, ebooks and audiobooks using Sonarr, Radarr, Lidarr and Readarr.

## Technologies
- [Docker Compose](https://docs.docker.com/compose/install/)
- [Sonarr](https://sonarr.tv/)
- [Radarr](https://radarr.video/)
- [Lidarr](https://lidarr.audio/)
- [Readarr](https://readarr.com/)

## How it works

### Preflight

The `preflight` service runs a [bash script](preflight/preflight) to check the `/srv/shared` is mounted. It does so by checking it contains at least one file.

Only after `preflight` exits successfully do the other services start due to:

```yaml
depends_on:
  preflight:
    condition: service_completed_successfully
```

### Sonarr

The `sonarr` service runs [Sonarr](https://sonarr.tv/) for automated downloading and organising of **TV shows**.

### Radarr

The `radarr` service runs [Radarr](https://radarr.video/) for automated downloading and organising of **movies**.

### Lidarr

The `lidarr` service runs [Lidarr](https://lidarr.audio/) for automated downloading and organising of **music**.

### Readarr

The `readarr` service runs [Readarr](https://readarr.com/) for automated downloading and organising of **ebooks**.

### Listenarr

The `listenarr` service runs [Readarr](https://readarr.com/) for automated downloading and organising of **audiobooks**.

> [!NOTE]
> Listenarr is a second instance of [Readarr](https://readarr.com/) because it's not possible to have both ebook and audiobook support in the same instance.

### Watch

The `watch` service provides a simple [read-only file server](https://caddyserver.com/docs/caddyfile/directives/file_server) of the Sonarr and Radarr directories.


## Getting started

### 1. Update the `preflight` environment variables

Copy the `preflight/.env.example` file to `preflight/.env` and set:
- `ALLOWED_IP` to your external IP address

Start the `preflight` service in attached mode to check it's set up correctly:

```bash
docker compose up preflight
```

### 2. Update the shared environment variables

User and group id are defined in two places:

Copy `.env.example` to `.env`:

```bash
cp .env.example .env
```

Copy `shared/.env.example` to `shared/.env`:

```bash
cp .env.example .env
```

Run the `id` command to determine the values your user id and group id. Typically, these will both be `1000` but they can vary.

Set the `USER_ID` and `PUID` to the user id number returned by the `id` command.

Set the `GROUP_ID` and `PGID` to the group id number returned by the `id` command.

For example if `id` returns:

```
uid=1234(your_user) gid=5678(your_group)
```

Then use:

```ini
USER_ID=1234
GROUP_ID=5678
```

> [!TIP]
> You can set the timezone value `TZ` to your [TZ identifier](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List), but it's best practice to stick with `UTC` for servers to reduce confusion when reading logs.

### 3. Start the preflight service

Preflight is going to check the `/srv/shared` directory so make sure it contains at least one file:

```bash
touch /srv/shared/.not-empty
```

Start up the `preflight` service in attached mode to check it's set up correctly:

```bash
docker compose up preflight
```

### 4. Start the remaining services

Start the remaining services in detached mode and wait for them to turn `Healthy`:

```bash
docker compose up -d --wait
```

### 5. Configure Sonarr, Radarr, Lidarr, Readarr and Listenarr

Each service is available on the following ports:

- Sonarr: http://localhost:8989
- Radarr: http://localhost:7878
- Lidarr: http://localhost:8686
- Readarr: http://localhost:8787
- Listenarr: http://localhost:8484

Set the root folder:

`Settings > Media Management > Add Root Folder` and set the respective path for the service:
- `/srv/shared/sonarr`
- `/srv/shared/radarr`
- `/srv/shared/lidarr`
- `/srv/shared/readarr`
- `/srv/shared/listenarr`

Set the indexers:
`Settings > Indexers > Add Indexer > Torznab` and and use the URL and API key from Prowlarr.

Set the download client:
`Settings > Download Clients > Add Client`.

### 6. Configure watch

Copy the `watch/.env.example` file to `watch/.env` and set:

- `AUTH_USER` and `AUTH_PASSWORD` to your desired username and password.
- `AUTH_USER2` and `AUTH_PASSWORD2` to your desired username and password.

Restart the `watch` service:

```bash
docker compose up -d watch
```

Watch should now be accessible

The index will show a generic message: http://localhost:11180

The Sonarr and Radarr directories will be available with basic authentication at:
- Sonarr: http://localhost:11180/tv
- Radarr: http://localhost:11180/movies

## Next steps

-  Part 2 [Steaming TV shows, movies and music with Plex and Jellyfin](https://github.com/RogueOneEcho/how-to-setup-plex-sonarr-radarr/tree/part-2)

## Troubleshooting

1. Check the logs
2. Re-read the guide
3. [Ask for help in GitHub Discussions](https://github.com/RogueOneEcho/how-to-setup-plex-sonarr-radarr/discussions)
4. [Create an issue](https://github.com/RogueOneEcho/how-to-setup-plex-sonarr-radarr/issues)
