# Home Assistant Kickstart

![image](assets/screenshot.png)

This repository provides a modular Docker Compose setup for quickly getting started with Home Assistant and related services. Each stack is organized into its own directory under `stack/`, making it easy to install, maintain, and extend your smart home infrastructure.

## Table of Contents
- [Google Assistant Integration](docs/google-home-assistant.md)

## Boilerplate
- [Home Assistant Configuration](stack/homeassistant/ha_config/)

## Directory Structure

- `stack/homeassistant/` – Core Home Assistant stack (Automation, MQTT, Zigbee, Node-RED, Portainer)
- `stack/data-logger/` – Data logging and visualization stack (InfluxDB, Grafana, MariaDB, Chronograf, phpMyAdmin)
- `stack/frigate/` – NVR and AI video analytics stack (Frigate)
- 
Each stack has its own `docker-compose.yml` and `.env` file for configuration.

## Stack Interactions

- **Networking:** All stacks connect to the same external Docker network (`homeassistant`), allowing seamless communication between services.
- **Data Flow:**
  - Home Assistant utilizes **EMQX** for MQTT messaging, **Zigbee2MQTT** for Zigbee device integration, and **Node-RED** for advanced automations.
  - **InfluxDB** and **MariaDB** (from `data-logger`) are used by Home Assistant for long-term data storage and the recorder function.
  - **Grafana** and **Chronograf** visualize data from InfluxDB.
  - **Frigate** (on its own `homeassistant` network) can integrate with Home Assistant for AI camera analysis and notifications via MQTT.
- **Management:** **Portainer** provides a web UI to manage all Docker containers.

## Quick Start

### Prerequisites

- Docker and Docker Compose installed.
- Create the external Docker network:
  ```bash
  docker network create homeassistant
  ```

### 1. Clone the Repository

```bash
git clone https://github.com/racksync/hass-kickstart.git
cd hass-kickstart
```

### 2. Configure Environment Variables

Copy and edit the `.env.sample` files in each stack directory:

```bash
cp stack/homeassistant/.env.sample stack/homeassistant/.env
cp stack/data-logger/.env.sample stack/data-logger/.env
cp stack/frigate/.env.sample stack/frigate/.env
# Edit each .env file as needed
```

### 3. Start the Stacks

You can start each stack independently:

```bash
# Core Home Assistant Stack
cd stack/homeassistant
docker compose up -d

# Data Logger Stack (Optional)
cd ../data-logger
docker compose up -d

# Frigate NVR Stack (Optional)
cd ../frigate
docker compose up -d
```

### 4. Access Services

- Home Assistant: http://localhost:8123
- Portainer: http://localhost:9000
- Node-RED: http://localhost:1880
- Grafana: http://localhost:3000
- Chronograf: http://localhost:8888
- phpMyAdmin: http://localhost:8081
- Frigate: http://localhost:5000

## Interaction Overview

- **Home Assistant** connects to **MariaDB** for recorder/history and **InfluxDB** for time-series data.
- **Grafana** and **Chronograf** visualize data from **InfluxDB**.
- **Node-RED** and **Home Assistant** use **EMQX** for MQTT-based automations.
- **Frigate** publishes AI camera events to MQTT, which Home Assistant can consume for automations and notifications.

## Customization

- Home Assistant configuration resides in `stack/homeassistant/ha_config/`.
- Each stack's `.env` file controls versions and credentials.
- You can add or remove stacks based on your needs.

## Credits

- [Home Assistant](https://www.home-assistant.io/)
- [Frigate NVR](https://frigate.video/)
- [RACKSYNC CO., LTD.](https://racksync.com)

## License

See [LICENSE.md](LICENSE.md) for details. Individual components are subject to their own licenses.

