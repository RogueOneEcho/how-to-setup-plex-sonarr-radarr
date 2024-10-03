# Use a custom domain and HTTPS/TLS with Caddy

This is part 3 of the series: [how to setup Plex, Sonarr, Radarr, etc](https://github.com/RogueOneEcho/how-to-setup-plex-sonarr-radarr).

This part adds Caddy to the stack making the services accessible via a custom domain with HTTPS/TLS encryption.

> [!NOTE]
> It is assumed you are using Cloudflare for DNS. If not then refer to the Caddy documentation for your provider:*
> - [How to use DNS provider modules in Caddy 2](https://caddy.community/t/how-to-use-dns-provider-modules-in-caddy-2/8148)
> - [Automatic HTTPS](https://caddyserver.com/docs/automatic-https)
> - [HTTPS quick-start](https://caddyserver.com/docs/quick-starts/https)
> - [acme_dns reference](https://caddyserver.com/docs/caddyfile/options#acme-dns)

## Technologies
- [Caddy](https://caddyserver.com/) as a reverse proxy.
- [Cloudflare](https://www.cloudflare.com/) for DNS validation.

## How it works

### Caddy

The `caddy` service runs [Caddy](https://caddyserver.com) which:
- makes the services accessible via a custom domain such as `plex.example.com`
- automatically obtains and renews HTTPS/TLS certificates from Let's Encrypt using [Cloudflare DNS validation](https://github.com/caddy-dns/cloudflare).

## Getting started

### 1. Create DNS records

Add `A` records for the subdomains you intend to use pointing to your server's IP address:
- `plex.example.com`
- `jellyfin.example.com`
- `sonarr.example.com`
- `radarr.example.com`
- `lidarr.example.com`
- `readarr.example.com`
- `listenarr.example.com`
- `watch.example.com`

> [!WARNING]
> Be aware that this is publicly exposing your IP address.
>
> For extra security there are a few options, but these are outside the scope of this guide:
>
> - use Cloudflare to hide your server's IP address by enabling the Cloudflare proxy.
> - [create a personal VPN](https://www.digitalocean.com/community/tutorials/how-to-set-up-wireguard-on-ubuntu-20-04) on your server and bind Docker to only use the local VPN IP address. That way your services are connectable whenever you're connected to your personal VPN.

### 2. Obtain a Cloudflare API token

[By following these steps](https://github.com/caddy-dns/cloudflare/blob/master/README.md#configuration)

### 3. Set the Caddy environment variables

Copy the `caddy/.env.example` file to `caddy/.env` and set:

- `CLOUDFLARE_API_TOKEN` to your Cloudflare API token
- `LETSENCRYPT_EMAIL` to your email address
- `PLEX_HOST` to the domain you want to use for Plex
- `JELLYFIN_HOST` to the domain you want to use for Jellyfin
- `SONARR_HOST` to the domain you want to use for Sonarr
- `RADARR_HOST` to the domain you want to use for Radarr
- `LIDARR_HOST` to the domain you want to use for Lidarr
- `READARR_HOST` to the domain you want to use for Readarr
- `LISTENARR_HOST` to the domain you want to use for Listenarr
- `WATCH_HOST` to the domain you want to use for Watch

### 4. Start the services

Start the services with:

```bash
docker compose up -d
```

Follow the `caddy` logs to ensure Caddy is able to complete the DNS challenge for a Let's Encrypt certificate:

```bash
docker compose logs -f caddy
```

Caddy doesn't use plain text logging so if you find that to confusing try this snippet to make the JSON readable:

```bash
docker logs caddy 2>&1 | jq --color-output | less -R
```

## Troubleshooting

1. Check the logs
2. Re-read the guide
3. [Ask for help in GitHub Discussions](https://github.com/RogueOneEcho/how-to-setup-plex-sonarr-radarr/discussions)
4. [Create an issue](https://github.com/RogueOneEcho/how-to-setup-plex-sonarr-radarr/issues)
