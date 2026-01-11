FROM python:3.11-slim-bookworm

ENV DEBIAN_FRONTEND=noninteractive LANG=C.UTF-8

# System-Pakete:
# - ALSA für arecord/aplay
# - avahi-utils (zeroconf)
# - git (um das Repo für examples/ zu klonen)
# - gpiozero + spidev (für LEDs)
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      alsa-utils \
      avahi-utils \
      git \
      python3-gpiozero \
      python3-spidev \
      python3-venv \
 && rm -rf /var/lib/apt/lists/*

# wyoming-satellite installieren (als Python-Paket, nicht über script/setup)
RUN pip install --no-cache-dir \
      "git+https://github.com/rhasspy/wyoming-satellite.git"

# Wrapper-Binary: 'wyoming-satellite' auf PATH
RUN printf '%s\n' \
    '#!/bin/sh' \
    'exec python -m wyoming_satellite "$@"' \
    > /usr/local/bin/wyoming-satellite \
 && chmod +x /usr/local/bin/wyoming-satellite

# Repo klonen, um an examples/2mic_service.py zu kommen
WORKDIR /app
RUN git clone --depth 1 https://github.com/rhasspy/wyoming-satellite.git /app/src

# Extra venv für das 2Mic-LED-Script
RUN python -m venv /app/examples-venv \
 && /app/examples-venv/bin/pip install --no-cache-dir --upgrade pip wheel setuptools \
 && /app/examples-venv/bin/pip install --no-cache-dir \
      "wyoming==1.5.2" \
      gpiozero \
      spidev

EXPOSE 10700 10500

# Standard-Entry: Satellite-Server
ENTRYPOINT ["wyoming-satellite"]
