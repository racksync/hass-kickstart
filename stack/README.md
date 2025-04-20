## Docker Stacks Overview

- `stack/homeassistant/` – Stack หลักของ Home Assistant (Automation, MQTT, Zigbee, Node-RED, Portainer)
- `stack/data-logger/` – Stack สำหรับ Data Logging และ Visualization (InfluxDB, Grafana, MariaDB, Chronograf, phpMyAdmin)
- `stack/frigate/` – Stack สำหรับ NVR และ AI Video Analytics (Frigate)

แต่ละ stack จะมีไฟล์ `docker-compose.yml` และ `.env` สำหรับการตั้งค่าของตัวเอง

## Directory Structure

- `stack/homeassistant/` – Stack หลักของ Home Assistant (Automation, MQTT, Zigbee, Node-RED, Portainer)
- `stack/data-logger/` – Stack สำหรับ Data Logging และ Visualization (InfluxDB, Grafana, MariaDB, Chronograf, phpMyAdmin)
- `stack/frigate/` – Stack สำหรับ NVR และ AI Video Analytics (Frigate)
- 
แต่ละ stack จะมีไฟล์ `docker-compose.yml` และ `.env` สำหรับการตั้งค่าของตัวเอง

## Stack Interactions

- **Networking:** ทุก stack จะเชื่อมต่อกับ external Docker network เดียวกัน (`homeassistant`) เพื่อให้บริการต่างๆ สื่อสารกันได้อย่างราบรื่น
- **Data Flow:**
  - Home Assistant ใช้ **EMQX** สำหรับ MQTT messaging, **Zigbee2MQTT** สำหรับเชื่อมต่ออุปกรณ์ Zigbee และ **Node-RED** สำหรับ automation ขั้นสูง
  - **InfluxDB** และ **MariaDB** (จาก `data-logger`) ถูกใช้โดย Home Assistant สำหรับเก็บข้อมูลระยะยาวและฟังก์ชัน recorder
  - **Grafana** และ **Chronograf** ใช้สำหรับ visualization ข้อมูลจาก InfluxDB
  - **Frigate** (บน network `homeassistant` ของตัวเอง) สามารถเชื่อมต่อกับ Home Assistant เพื่อวิเคราะห์กล้องด้วย AI และแจ้งเตือนผ่าน MQTT
- **Management:** **Portainer** ให้ web UI สำหรับจัดการ Docker containers ทั้งหมด

## Quick Start

### Prerequisites

- ติดตั้ง Docker และ Docker Compose
- สร้าง external Docker network:
  ```bash
  docker network create homeassistant
  ```

### 1. Clone the Repository

```bash
git clone https://github.com/racksync/hass-kickstart.git
cd hass-kickstart
```

### 2. Configure Environment Variables

คัดลอกและแก้ไขไฟล์ `.env.sample` ในแต่ละ stack directory:

```bash
cp stack/homeassistant/.env.sample stack/homeassistant/.env
cp stack/data-logger/.env.sample stack/data-logger/.env
cp stack/frigate/.env.sample stack/frigate/.env
# แก้ไขแต่ละไฟล์ .env ตามต้องการ
```

### 3. Start the Stacks

คุณสามารถ start แต่ละ stack ได้อย่างอิสระ:

```bash
# Core Home Assistant Stack
cd stack/homeassistant
docker compose up -d

# Data Logger Stack (ถ้าต้องการ)
cd ../data-logger
docker compose up -d

# Frigate NVR Stack (ถ้าต้องการ)
cd ../frigate
docker compose up -d
```

### 4. Access Services

- Home Assistant: http://localhost:8123
- EMQX: http://localhost:8083
- Zigbee2MQTT: http://localhost:8080
- Portainer: http://localhost:9000
- Node-RED: http://localhost:1880
- Grafana: http://localhost:3000
- Chronograf: http://localhost:8888
- phpMyAdmin: http://localhost:8081
- Frigate: http://localhost:5000

## Interaction Overview

- **Home Assistant** เชื่อมต่อกับ **MariaDB** สำหรับ recorder/history และ **InfluxDB** สำหรับ time-series data
- **Grafana** และ **Chronograf** ใช้สำหรับ visualization ข้อมูลจาก **InfluxDB**
- **Node-RED** และ **Home Assistant** ใช้ **EMQX** สำหรับ automation ผ่าน MQTT
- **Frigate** ส่ง event จาก AI camera ไปยัง MQTT ซึ่ง Home Assistant สามารถนำไปใช้สำหรับ automation และ notification

## Customization

- การตั้งค่า Home Assistant อยู่ใน `stack/homeassistant/ha_config/`
- ไฟล์ `.env` ของแต่ละ stack ใช้สำหรับกำหนดเวอร์ชันและ credentials
- คุณสามารถเพิ่มหรือลบ stack ได้ตามต้องการ