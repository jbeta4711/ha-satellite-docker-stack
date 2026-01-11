FROM debian:bookworm-slim

ENV DEBIAN_FRONTEND=noninteractive LANG=C.UTF-8

# Python + venv + GPIO + SPI
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      python3 \
      python3-venv \
      python3-gpiozero \
      python3-spidev \
 && rm -rf /var/lib/apt/lists/*

# Eigenes venv für wyoming, ABER mit --system-site-packages,
# damit gpiozero/spidev aus den Debian-Paketen sichtbar sind.
RUN python3 -m venv /opt/wyoming-venv --system-site-packages \
 && /opt/wyoming-venv/bin/pip install --upgrade pip \
 && /opt/wyoming-venv/bin/pip install --no-cache-dir "wyoming==1.5.2"

WORKDIR /app

# Das Script kommt per Volume nach /app/examples (siehe docker-compose)
ENTRYPOINT ["/opt/wyoming-venv/bin/python", "/app/examples/2mic_service.py"]
CMD ["--uri", "tcp://0.0.0.0:10500"]
