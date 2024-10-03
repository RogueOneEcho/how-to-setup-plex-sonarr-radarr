# Steaming TV shows, movies and music with Plex and Jellyfin

This is part 2 of the series: [how to setup Plex, Sonarr, Radarr, etc](https://github.com/RogueOneEcho/how-to-setup-plex-sonarr-radarr).

This part adds streaming using the Plex and Jellyfin media servers.

## Technologies
- [Plex](https://www.plex.tv/) media server.
- [Jellyfin](https://jellyfin.org/) an open-source alternative to Plex

## How it works

### Plex

The `plex` service runs [Plex](https://www.plex.tv/) for streaming your personal collection of movies, TV and music.

### Jellyfin

The `jellyfin` service runs [Jellyfin](https://jellyfin.org/) for streaming your personal collection of movies, TV and music. It's an open-source alternative to Plex.

> [!NOTE]
> As Jellyfin and Plex serve the same purposes the `jellyfin` service is defined as a separate [profile](https://docs.docker.com/compose/how-tos/profiles/) in the `docker-compose.yml` file and won't start up by default. Unless specifically called with:
>
> ```bash
> docker compose --profile jellyfin up -d
> ```

## Getting started

### 1. Start Plex

Start the `plex` service with:

```bash
docker compose up -d --wait plex
```

Follow the logs to ensure Plex is running smoothly:

```bash
docker compose logs -f plex
```

Plex should now be accessible at `https://localhost:32400`.

### 2. Start Jellyfin (optional)

To start Jellyfin via its profile:

```bash
docker compose up --profile jellyfin -d --wait jellyfin
```

If you'd rather not have Jellyfin defined as a separate profile then in `docker-compose.yml` remove the `profiles` section so it starts with the other services.

```yaml
    profiles:
    - jellyfin
```

Follow the logs to ensure Jellyfin is running smoothly:

```bash
docker compose logs -f jellyfin
```

Plex should now be accessible at `https://localhost:8096`.

### 3. Set up libraries

Regardless of whether you're using Plex or Jellyfin you'll need to set up libraries through there respective web interfaces:

- TV Shows are managed by Sonarr so set the library path to `/srv/shared/sonarr`
- Movies are managed by Radarr so set the library path to `/srv/shared/radarr`
- Music is managed by Lidarr so set the library path to `/srv/shared/lidarr`
- Audiobooks are managed by Listenarr so set the library path to `/srv/shared/listenarr`

## Next steps

-  Part 3 [Use a custom domain and HTTPS/TLS with Caddy](https://github.com/RogueOneEcho/how-to-setup-plex-sonarr-radarr/tree/part-3)

## Troubleshooting

1. Check the logs
2. Re-read the guide
3. [Ask for help in GitHub Discussions](https://github.com/RogueOneEcho/how-to-setup-plex-sonarr-radarr/discussions)
4. [Create an issue](https://github.com/RogueOneEcho/how-to-setup-plex-sonarr-radarr/issues)
