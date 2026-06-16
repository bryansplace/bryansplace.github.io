# Chromecasting Immich Kiosk with CATT

## Introduction

[Immich](https://immich.app) is a popular self-hosted alternative to Google Photos. [Immich Kiosk](https://github.com/damongolding/immich-kiosk) provides a highly configurable slideshow interface for displaying Immich photos on browsers and devices.

Casting to a [Google Chromecast device](https://docs.immich.app/features/casting) is possible but non-trivial, as the required HTTPS connection demands the configuration of SSL certificates and custom DNS entries.

[CATT (Cast All The Things)](https://github.com/skorokithakis/catt) offers a simpler path, enabling a local website to be cast directly to a Chromecast device on the same LAN. This guide describes how to use CATT to cast Immich Kiosk to a local Chromecast device.

---

## Prerequisites

- A running Immich Kiosk instance
- A host system with Docker installed

Familiarity with Docker is assumed, given that Immich itself is typically run as a container.

---

## CATT

CATT allows websites to be rendered on Chromecast devices, as documented in the [CATT repository](https://github.com/skorokithakis/catt). Rather than installing CATT directly on the host system, the approach below uses a Docker container to keep the host environment clean.

---

## Step 1 — Create a Dockerfile

Create the following `Dockerfile` in a new directory:

```dockerfile
FROM python:3.11-slim
RUN pip install --no-cache-dir catt
ENTRYPOINT ["catt"]
CMD ["--help"]
```

---

## Step 2 — Build the Image

```bash
docker build -t catt .
```

---

## Step 3 — Scan for Chromecast Devices

Run the container with the `scan` command to list all Chromecast devices discovered on the local network:

```bash
docker run --net=host --rm catt scan
```

> **Notes:**
> - `--net=host` is required to give the container full access to the local network.
> - `--rm` removes the container automatically upon completion.

---

## Step 4 — Cast Kiosk to a Specific Device

The `cast_site` command, combined with the `-d` option, casts an Immich Kiosk URL to a named Chromecast device. As documented in the [Kiosk documentation](https://github.com/damongolding/immich-kiosk), URL query parameters can be used to customise the display at runtime, overriding the values set in `config.yaml` or the Docker Compose environment.

**Example:**

Given an Immich Kiosk instance running at `192.168.68.111:3000`, the following command casts to a device named `Salon`. It displays images of two specific people taken between January 1982 and February 2000, with a 15-second slide duration and a fade transition:

```bash
docker run --net=host --rm catt \
  -d 'Salon' \
  cast_site 'http://192.168.68.111:3000?duration=15&transition=fade&person=25204c5c-e232-4488-b97c-c3f06f5cd195&person=25204c5c-e232-4488-b97c-c3f06f5cd196&date_filter=1982-01-01_to_2000-02-01'
```

The backslash line-continuation characters improve readability but the full command can equally be written on a single line.

---


