# SharpTools Bridge

SharpTools Bridge is a local bridge runtime for connecting LAN devices and local smart home protocols to SharpTools.

The bridge runs on your own hardware, exposes a local web UI for setup and management, and connects outbound to SharpTools Cloud. Docker is the recommended alpha deployment path.

> SharpTools Bridge is currently in alpha. Expect device support and setup flows to expand as testing continues.

## Quick Start With Docker

On Linux, host networking is recommended so local smart home protocols can use LAN discovery, mDNS, UDP, and direct device access.

```sh
docker run -d \
  --name sharptools-bridge \
  --restart unless-stopped \
  --network host \
  -v sharptools-bridge-data:/data \
  ghcr.io/sharptools-io/bridge:alpha
```

Then open the local bridge UI:

```text
http://<bridge-host>:8787/
```

The `/data` mount stores bridge settings, logs, driver data, Matter state, and pairing credentials. Keep this volume when updating the container.

## Docker Desktop Or Port Mapping

If host networking is unavailable, publish the admin port instead:

```sh
docker run -d \
  --name sharptools-bridge \
  --restart unless-stopped \
  -p 8787:8787 \
  -v sharptools-bridge-data:/data \
  ghcr.io/sharptools-io/bridge:alpha
```

Port-mapped mode is fine for the local web UI and outbound SharpTools Cloud connection, but some LAN discovery and local protocol features may be limited compared with Linux host networking.

## Docker Compose

This repository includes a Docker Compose example:

- [docker-compose.yml](./docker-compose.yml)

Start the bridge:

```sh
docker compose up -d
```

Update the bridge:

```sh
docker compose pull
docker compose up -d
```

## Updating A `docker run` Install

Pull the newer image and recreate the container while keeping the same data volume:

```sh
docker pull ghcr.io/sharptools-io/bridge:alpha
docker stop sharptools-bridge
docker rm sharptools-bridge
docker run -d \
  --name sharptools-bridge \
  --restart unless-stopped \
  --network host \
  -v sharptools-bridge-data:/data \
  ghcr.io/sharptools-io/bridge:alpha
```

## Configuration

Common environment variables:

```text
SHARPTOOLS_BRIDGE_HOME=/data
SHARPTOOLS_BRIDGE_HOST=0.0.0.0
SHARPTOOLS_BRIDGE_PORT=8787
SHARPTOOLS_BRIDGE_UPDATE_CHANNEL=alpha
```

The released Docker image uses SharpTools production cloud endpoints by default. Most users should not need to configure SharpTools API or socket URLs manually.

## Current Integration Focus

The alpha bridge currently focuses on:

- Matter devices that are already reachable on the local network through Matter multi-admin sharing
- Hue-compatible bridge discovery and local light control
- Local bridge onboarding, cloud pairing, logs, update checks, and device sync management

Matter commissioning of brand-new Wi-Fi devices usually requires a phone or platform commissioner first. After the device is on your network and shared through Matter multi-admin, SharpTools Bridge can add it to the SharpTools fabric.

## Releases

Release downloads and checksums are published on the [GitHub Releases page](https://github.com/sharptools-io/bridge/releases).

Docker images are published to GitHub Container Registry:

```text
ghcr.io/sharptools-io/bridge:alpha
```

Tagged versions are also available, for example:

```text
ghcr.io/sharptools-io/bridge:0.1.1
```
