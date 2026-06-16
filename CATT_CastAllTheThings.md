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

## Simplifying the Command

The CATT command becomes unwieldy as more URL parameters are added. Wrapping it in a shell script is a clean and maintainable solution.

**`cast-salon.sh`:**

```bash
#!/bin/bash
KIOSK_HOST="http://192.168.68.111:3000"
DEVICE="Salon"
PARAMS="duration=15&transition=fade\
&person=25204c5c-e232-4488-b97c-c3f06f5cd195\
&person=25204c5c-e232-4488-b97c-c3f06f5cd196\
&date_filter=1982-01-01_to_2000-02-01"

docker run --net=host --rm catt -d "$DEVICE" cast_site "${KIOSK_HOST}?${PARAMS}"
```

Make it executable and run it:

```bash
chmod +x cast-salon.sh
./cast-salon.sh
```

Multiple scripts can be maintained for different devices or display configurations — for example, a family room slideshow showing all albums, and a bedroom display filtered to specific people or date ranges.

---

## "Hey Google" Voice Trigger

### Is It Still Supported?

**Yes — but with a key limitation that affects this use case.**

Google Home Routines support custom voice trigger phrases (e.g. *"Hey Google, start the photo frame"*), configured via the Google Home app under **Routines → + → When I say to Google Assistant**. However, Routines are designed to automate smart home devices and Google services. There is no built-in action type capable of executing a shell command or running a Docker container on a local server.

> **Note:** Google is actively migrating Google Home from Google Assistant to Gemini for Home. Some existing voice automations have been reported as broken during this transition, so anyone building on this approach should be prepared for occasional maintenance as the platform evolves.

### A Workaround

A voice trigger can be connected to the CATT command via a two-step bridge:

1. **Expose a local HTTP endpoint.** A lightweight tool such as [webhook](https://github.com/adnanh/webhook) or a simple Python Flask/FastAPI application can listen for an incoming HTTP request and execute the CATT shell script in response.

2. **Trigger the endpoint from a Google Routine via IFTTT.** IFTTT's Google Assistant integration supports custom voice trigger phrases and can fire an HTTP webhook as an action, bridging the voice command to the local endpoint.

The resulting chain is:

```
Voice command → IFTTT webhook → local HTTP endpoint → shell script → Docker CATT command
```

This approach is functional, but it adds meaningful complexity and introduces dependencies on both IFTTT and an always-on local HTTP service. For most self-hosted setups, one of the simpler alternatives below is likely more reliable:

| Alternative | Description |
|---|---|
| **Shell script shortcut** | Run `./cast-salon.sh` from a terminal or desktop launcher |
| **Cron job** | Schedule casting at a fixed time using `crontab` |
| **Home Assistant button** | Trigger the script via a dashboard button or automation in Home Assistant |
| **Scheduled Docker restart** | Use `docker run` inside a cron job for a time-based slideshow |

---

## Reference: Useful Kiosk URL Parameters

The following query parameters can be appended to the Kiosk URL to customise the display without modifying `config.yaml`:

| Parameter | Example | Description |
|---|---|---|
| `duration` | `duration=30` | Slide display duration in seconds |
| `transition` | `transition=fade` | Transition style: `none`, `fade`, or `cross-fade` |
| `person` | `person=<uuid>` | Filter by person ID (repeatable) |
| `album` | `album=<uuid>` | Filter by album ID (repeatable) |
| `date_filter` | `date_filter=2000-01-01_to_2010-12-31` | Restrict to a date range |
| `show_image_date` | `show_image_date=true` | Overlay the image date |
| `show_image_location` | `show_image_location=true` | Overlay the image location |

A full list of supported parameters is available in the [Immich Kiosk documentation](https://github.com/damongolding/immich-kiosk).
