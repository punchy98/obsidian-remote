# obsidian-remote
![GitHub release (latest by date)](https://img.shields.io/github/v/release/punchy98/obsidian-remote?style=plastic)

This docker image allows you to run obsidian in docker as a container and access it via your web browser.

Use `http://localhost:8080/` to access it locally, do not expose this to the web unless you secure it and know what you are doing!!

## Using the Container

To run a interactive version to test it out. 

```
docker run --rm -it -v /obsidian/vaults:/vaults -v /obsidian/config:/config -p 8080:8080 ghcr.io/punchy98/obsidian-remote:latest
```

To run it as a daemon in the background.

```
docker run -d -v /obsidian/vaults:/vaults -v /obsidian/config:/config -p 8080:8080 ghcr.io/punchy98/obsidian-remote:latest
```

### Ports

| Port  | Description                             |
| ----- | --------------------------------------- |
| 8080  | Obsidian Web Interface                  |
| 27123 | Local REST API Plugin HTTP Server Port  |
| 27124 | Local REST API Plugin HTTPS Server Port |

### Mapped Volumes

| Path      | Description                                                               |
| --------- | ------------------------------------------------------------------------- |
| `/vaults` | The location on the host for your Obsidian Vaults                         |
| `/config` | The location to store Obsidan configuration and ssh data for obsidian-git |

### Environment Variables

| Environment Variable | Description                                                                                                                                                                                            |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| PUID                 | Set the user ID for the container user. `911` by default.                                                                                                                                              |
| PGID                 | Set the group ID for the continer user. `911` by default.                                                                                                                                              |
| TZ                   | Set the Time Zone for the container, should match your TZ. `Etc/UTC` by default. See [List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) for valid options. |
| DOCKER_MODS          | Use to add mods to the container like git. E.g. `DOCKER_MODS=linuxserver/mods:universal-git` See [Docker Mods](https://github.com/linuxserver/docker-mods) for details.                                |
| KEYBOARD             | Used to se the keyboard being used for input. E.g. `KEYBOARD=en-us-qwerty` or `KEYBOARD=de-de-qwertz` a list of other possible values (not tested) can be found at https://github.com/linuxserver/docker-digikam#keyboard-layouts |

## Using Docker Compose

```YAML
version: '3.8'
services:
  obsidian:
    image: 'ghcr.io/punchy98/obsidian-remote:latest'
    container_name: obsidian-remote
    restart: unless-stopped
    ports:
      - 8080:8080
    volumes:
      - /obsidian/vaults:/vaults
      - /obsidian/config:/config
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Los_Angeles
      - DOCKER_MODS=linuxserver/mods:universal-git
```

## Enabling GIT for the obsidian-git plugin

This container uses the base images from linuxserver.io. This means you can the linuxserver.io mods. To add support for git add the `DOCKER_MODS` environment variable like so `DOCKER_MODS=linuxserver/mods:universal-git`.

### Docker CLI example

```
docker run -d -v /obsidian/vaults:/vaults -v /obsidian/config:/config -p 8080:8080 -e DOCKER_MODS=linuxserver/mods:universal-git ghcr.io/punchy98/obsidian-remote:latest
```

## Reloading Obsidan in the Browser

If you make changes to plugins or do updates that need to have obsidian restarted, instead of having to stop and start the docker container you can just close the Obsidian UI and right click to show the menus and reopen it. 

## Setting PUID and PGID

To set PUID and PGID use the follow environment variables on the command line, by default the IDs are 911/911

```
docker run --rm -it -v /obsidian/vaults:/vaults -v /obsidian/config:/config -e PUID=1000 -e PGID=1000 -p 8080:8080 ghcr.io/punchy98/obsidian-remote:latest
```

Or, if you use docker-compose, add them to the environment: section:

```yaml
environment:
  - PUID=1000
  - PGID=1000
```

## Building locally

To build and use it locally run the following commands:

```
docker build --pull --rm -f Dockerfile -t obsidian-remote:latest .
```

To run the locally build image:

```
docker run --rm -it -v /obsidian/vaults:/vaults -v /obsidian/config:/config -p 8080:8080 obsidian-remote:latest bash
```
