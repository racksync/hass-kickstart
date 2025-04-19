# Home Assistant Kickstart ด้วย Docker Compose (Modular Stacks)

[image](assets/screenshot.png)

Repository นี้มี Docker Compose แบบโมดูลาร์สำหรับเริ่มต้นใช้งาน Home Assistant และบริการที่เกี่ยวข้องอย่างรวดเร็ว แต่ละ stack ถูกจัดระเบียบไว้ในไดเรกทอรีของตัวเองภายใต้ `stack/` ทำให้ง่ายต่อการติดตั้ง บำรุงรักษา และขยายโครงสร้างพื้นฐานบ้านอัจฉริยะของคุณ

## โครงสร้างไดเรกทอรี

- `stack/homeassistant/` – Stack หลักของ Home Assistant (ระบบอัตโนมัติ, MQTT, Zigbee, Node-RED, Portainer)
- `stack/data-logger/` – Stack สำหรับบันทึกและแสดงข้อมูล (InfluxDB, Grafana, MariaDB, Chronograf, phpMyAdmin)
- `stack/frigate/` – Stack สำหรับ NVR และการวิเคราะห์วิดีโอด้วย AI (Frigate)

แต่ละ stack มีไฟล์ `docker-compose.yml` และ `.env` ของตัวเองสำหรับการกำหนดค่า

## การทำงานร่วมกันของ Stacks

- **เครือข่าย:** ทุก stack (ยกเว้น Frigate ซึ่งใช้เครือข่ายของตัวเอง) เชื่อมต่อกับเครือข่าย Docker ภายนอกเดียวกัน (`homeassistant`) ทำให้บริการต่างๆ สามารถสื่อสารกันได้อย่างราบรื่น
- **การไหลของข้อมูล:**
  - Home Assistant ใช้ **EMQX** สำหรับการส่งข้อความ MQTT, **Zigbee2MQTT** สำหรับการรวมอุปกรณ์ Zigbee และ **Node-RED** สำหรับระบบอัตโนมัติขั้นสูง
  - **InfluxDB** และ **MariaDB** (จาก `data-logger`) ถูกใช้โดย Home Assistant สำหรับการจัดเก็บข้อมูลระยะยาวและฟังก์ชัน recorder
  - **Grafana** และ **Chronograf** แสดงข้อมูลจาก InfluxDB
  - **Frigate** (ในเครือข่าย `backend` ของตัวเอง) สามารถรวมเข้ากับ Home Assistant สำหรับการวิเคราะห์กล้องด้วย AI และการแจ้งเตือนผ่าน MQTT
- **การจัดการ:** **Portainer** มีเว็บ UI สำหรับจัดการ Docker containers ทั้งหมด

## เริ่มต้นใช้งานอย่างรวดเร็ว

### ข้อกำหนดเบื้องต้น

- ติดตั้ง Docker และ Docker Compose แล้ว
- สร้างเครือข่าย Docker ภายนอก:
  ```bash
  docker network create homeassistant
  docker network create backend
  ```

### 1. โคลน Repository

```bash
git clone https://github.com/racksync/hass-kickstart.git
cd hass-kickstart
```

### 2. กำหนดค่าตัวแปรสภาพแวดล้อม

คัดลอกและแก้ไขไฟล์ `.env.sample` ในแต่ละไดเรกทอรีของ stack:

```bash
cp stack/homeassistant/.env.sample stack/homeassistant/.env
cp stack/data-logger/.env.sample stack/data-logger/.env
cp stack/frigate/.env.sample stack/frigate/.env
# แก้ไขไฟล์ .env แต่ละไฟล์ตามต้องการ
```

### 3. เริ่มใช้งาน Stacks

คุณสามารถเริ่มแต่ละ stack ได้อย่างอิสระ:

```bash
# Stack หลักของ Home Assistant
cd stack/homeassistant
docker compose up -d

# Stack สำหรับบันทึกข้อมูล (ทางเลือก)
cd ../data-logger
docker compose up -d

# Stack Frigate NVR (ทางเลือก)
cd ../frigate
docker compose up -d
```

### 4. เข้าถึงบริการต่างๆ

- Home Assistant: http://localhost:8123
- Portainer: http://localhost:9000
- Node-RED: http://localhost:1880
- Grafana: http://localhost:3000
- Chronograf: http://localhost:8888
- phpMyAdmin: http://localhost:8081
- Frigate: http://localhost:5000

## ภาพรวมการทำงานร่วมกัน

- **Home Assistant** เชื่อมต่อกับ **MariaDB** สำหรับ recorder/history และ **InfluxDB** สำหรับข้อมูล time-series
- **Grafana** และ **Chronograf** แสดงข้อมูลจาก **InfluxDB**
- **Node-RED** และ **Home Assistant** ใช้ **EMQX** สำหรับระบบอัตโนมัติผ่าน MQTT
- **Frigate** เผยแพร่เหตุการณ์จากกล้อง AI ไปยัง MQTT ซึ่ง Home Assistant สามารถนำไปใช้สำหรับระบบอัตโนมัติและการแจ้งเตือน

## การปรับแต่ง

- การกำหนดค่า Home Assistant อยู่ใน `stack/homeassistant/ha_config/`
- ไฟล์ `.env` ของแต่ละ stack ควบคุมเวอร์ชันและข้อมูลประจำตัว
- คุณสามารถเพิ่มหรือลบ stack ได้ตามความต้องการใช้งานของคุณ

## เครดิต

- [Home Assistant](https://www.home-assistant.io/)
- [Frigate NVR](https://frigate.video/)
- [RACKSYNC CO., LTD.](https://racksync.com)

## ใบอนุญาต

ดูรายละเอียดใน [LICENSE.md](LICENSE.md) ส่วนประกอบแต่ละอย่างอยู่ภายใต้ใบอนุญาตของตัวเอง

