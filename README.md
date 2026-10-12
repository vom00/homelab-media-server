# homelab-media-server

My home media server. It runs in Docker containers on a Linux machine and streams movies and shows to any device on my network.

The library comes from Blu-ray discs I already own.

## How I set it up

I installed the server with [YAMS](https://yams.media) (Yet Another Media Server), an install script that sets up a media server in Docker. This repo is a cleaned-up version of that setup with only the parts I use. It has no downloaders and no VPN, and every personal value moved into a `.env` file that is never committed.

## AI help

AI (Claude) wrote the files in this repo, using the YAMS docs and source as the guide.

## What runs

| Container | What it does | Web page |
| --- | --- | --- |
| `jellyfin` (or `emby` / `plex`) | The media server. Streams the library to TVs, phones and browsers. | `http://<server>:8096` (Plex uses `:32400/web`) |
| `portainer` | A web page for checking on the containers and reading their logs. | `http://<server>:9000` |
| `watchtower` | Pulls new images and restarts containers so they stay updated. | none |

## Files

| File | What it is |
| --- | --- |
| [`docker-compose.yml`](docker-compose.yml) | Defines the three containers. Every personal value comes from `.env`. |
| [`.env.example`](.env.example) | Template for your settings. Copy it to `.env` and fill it in. |
| [`.gitignore`](.gitignore) | Keeps `.env`, app data, logs and media files out of git. |

## Values to fill in

Copy the template, then edit `.env`:

```bash
cp .env.example .env
```

| Variable | What to put there |
| --- | --- |
| `PUID` / `PGID` | The user and group IDs that own your media files. Run `id` on the server to see them. |
| `TZ` | Your time zone, like `America/Chicago`. |
| `MEDIA_DIRECTORY` | The folder that holds your `movies/` and `tvshows/` folders. |
| `INSTALL_DIRECTORY` | The folder where this repo lives on the server. App settings get saved in `config/` inside it. |
| `MEDIA_SERVICE` | `jellyfin` (default), `emby` or `plex`. |

## How to run it

You need a Linux machine with Docker and Docker Compose.

```bash
git clone https://github.com/vom00/homelab-media-server.git
cd homelab-media-server
cp .env.example .env      # then fill in your values
mkdir -p config
docker compose up -d
```

Then open `http://<server>:8096` and follow the media server's setup steps. Point the libraries at `/data/movies` and `/data/tvshows`.

Media folder layout:

```
<MEDIA_DIRECTORY>/
  movies/
    Movie Name (Year)/Movie Name (Year).mkv
  tvshows/
    Show Name/Season 01/Show Name S01E01.mkv
```

Useful commands:

```bash
docker compose ps          # what is running
docker compose logs -f     # live logs
docker compose pull        # get new images by hand
docker compose down        # stop everything
```

### Using Plex instead

Set `MEDIA_SERVICE=plex` in `.env`. In `docker-compose.yml`, uncomment `network_mode: host` and remove the `ports` block under `media`. Plex then answers on port 32400.

## What I learned

TODO (Vyom): write this part yourself.

## Credit

The layout and container choices follow [YAMS](https://yams.media) by rogs ([source](https://github.com/rogsme/yams)).
