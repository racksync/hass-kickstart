# Advanced Home Assistant Docker Compose Stack

This repository contains a modular Docker Compose setup for running Home Assistant with complementary services, separated into two distinct compose files for better flexibility and resource management.

## Stack Components

### Main Stack (`docker-compose.yml`)
The core components needed for home automation:
- **Portainer**: Docker container management UI
- **Home Assistant**: Open-source home automation platform
- **EMQX**: Scalable MQTT message broker
- **Node-RED**: Visual programming for automation workflows
- **Zigbee2MQTT**: Bridge for connecting Zigbee devices to MQTT

### Data Logger Stack (`docker-compose-data-logger.yml`) - Optional
Optional components for data storage, visualization, and management:
- **InfluxDB**: Time series database for metrics storage
- **Grafana**: Dashboard visualization platform
- **MariaDB**: Relational database for Home Assistant
- **phpMyAdmin**: Web interface for MariaDB management
- **Chronograf**: Data visualization for InfluxDB

## How the Stacks Work Together

The modular approach provides several benefits:

1. **Flexible Deployment**: Start with the main stack for basic functionality and add the data logger stack when needed.
2. **Resource Optimization**: Run the data-intensive components on separate hardware if necessary.
3. **Independent Maintenance**: Update, restart, or troubleshoot one stack without disrupting the other.

Both stacks use the same Docker network (`homeassistant`) for seamless communication:
- Home Assistant can store historical data in InfluxDB and MariaDB
- Grafana can visualize data from InfluxDB
- Node-RED can process MQTT messages from EMQX and interact with all components

## Installation & Usage

### Prerequisites
- Docker and Docker Compose installed
- Create a `.env` file with required variables (see example below)

### Deploy the Main Stack
```bash
docker compose -f docker-compose.yml up -d
```

### Deploy the Data Logger Stack (Optional)
```bash
docker compose -f docker-compose-data-logger.yml up -d
```

### Accessing Services
- Home Assistant: http://localhost:8123
- Portainer: http://localhost:9000
- Node-RED: http://localhost:1880
- Grafana: http://localhost:3000
- phpMyAdmin: http://localhost:8081
- Chronograf: http://localhost:8888

## Integration Setup

1. **Configure Home Assistant with MariaDB**:
   Add to your Home Assistant configuration.yaml:
   ```yaml
   recorder:
     db_url: mysql://user:password@mariadb/ha
   ```

2. **Connect Home Assistant to InfluxDB**:
   Add to your configuration.yaml:
   ```yaml
   influxdb:
     host: influxdb
     port: 8086
     database: db0
     username: !secret influxdb_user
     password: !secret influxdb_password
   ```

3. **Create Grafana dashboards** using InfluxDB as a data source to visualize Home Assistant metrics.

## Issue 

Please check the issue tracker for similar issues before creating one.

## Credits

- [Home Assistant Core](https://www.home-assistant.io/faq/ha-vs-hassio/)
- [Home Assistant Docker Images](https://hub.docker.com/r/homeassistant/home-assistant)

## Home Automation Consultant Services
- [RACKSYNC CO., LTD.](https://racksync.com)

[screenshot]: assets/screenshot.png "Home Assistant Training Course by RACKSYNC CO., LTD."

## License Agreement
By using this stack, you agree to the terms and conditions of the respective licenses for each of the components included in the stack.

