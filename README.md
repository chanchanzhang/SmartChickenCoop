# 🐔 Smart Chicken Coop Monitoring & Control System

## 📄 Background

With the rising cost of eggs and growing interest in self-sustainable living, many households are turning to backyard poultry farming. According to USDA data, egg prices have fluctuated significantly in recent years, driving consumers to seek alternatives such as raising chickens at home. As urban and suburban homeowners join the trend, there's a growing demand for automation solutions that reduce the manual burden of managing chicken coops.

This project addresses that demand by creating a cloud-connected, automated chicken coop system to monitor and control essential functions like door movement and environmental conditions, with added support for remote access and video streaming.

---

## 🌟 Goals

- 🚪 Automatically open/close the chicken coop door based on light levels or a set schedule
- 🌡️ Monitor and log environmental conditions (temperature, humidity, light)
- 💾 Store all sensor data in a time-series database for review
- 📣 Generate alerts if anomalies are detected (e.g., door stuck, extreme conditions)
- 📹 Stream live video from the coop via AWS Kinesis Video Streams
- 💻 Provide a secure web dashboard for real-time status and control

---

## 📊 Target Users

- **Urban/Suburban Backyard Chicken Owners**
- **Small Hobby Farmers**
- **Educational Institutions** with poultry science projects
- **DIY Smart Home Enthusiasts**
- **AgriTech Developers and Tinkerers**

---

## 📋 Project Requirements

### Functional Requirements
1. **Door Automation**
   - Automatic open/close based on light sensor or time schedule
   - Manual override from dashboard
2. **Environmental Monitoring**
   - Capture temperature, humidity, and light levels
3. **Cloud Logging**
   - Store time-stamped sensor data in DynamoDB
4. **Live Video Feed**
   - Stream via Kinesis Video Streams and embed in dashboard
5. **Web Dashboard**
   - Real-time display of sensor values and system status
   - Control interface for door actions
6. **Alert Notifications**
   - Triggered via AWS SNS on unusual conditions

### Non-Functional Requirements
- Secure user access via AWS Cognito
- Scalability to support multiple coops/users
- Reliability in all weather conditions
- Energy efficient (with optional solar extension)

---

## 🧱 System Architecture

```
   📊 Raspberry Pi (Sensors, Motor, Camera)
       |         \
   MQTT Publish   Video Stream
       |              \
       v               v
 ┌──────────────┐      ┌────────────────────────┐
 │ AWS IoT Core │<────▶│ AWS Kinesis Video Stream│
 └──────┬───────┘      └────────────────────────┘
        |
        | IoT Rule
        v
 ┌──────────────┐
 │ AWS Lambda   │──▶ Save data
 └──────┬───────┘
        v
 ┌─────────────┐
 │ DynamoDB    │
 └─────────────┘

     ▲                 ▲
     | API Gateway     | Cognito Auth
     v                 |
┌──────────────┐       |
│ React Web    │<──────┘
│ Dashboard    │
└──────────────┘
```

---

## 🔧 Hardware Design

### Main Controller
- **Raspberry Pi 4**: Central unit for sensor reading, motor control, and video streaming

### Sensors
- **DHT22**: Measures temperature and humidity
- **BH1750 or LDR**: Measures ambient light
- **Limit Switches**: Detect door position (open/closed)

### Actuator
- **DC motor or servo motor** with driver (e.g., L298N)
- **Limit switches** to detect end of travel

### Camera
- **Raspberry Pi Camera Module v2** (preferred for quality and RPi compatibility)
  - Resolution: 720p+ recommended
  - Low-light visibility preferred
  - USB webcam as fallback

---

## ☁️ AWS Services Overview

| Service                 | Function                                       |
|------------------------|------------------------------------------------|
| AWS IoT Core           | Secure MQTT communication with the Pi         |
| AWS Lambda             | Ingest and process telemetry data             |
| DynamoDB               | Store time-series sensor data                 |
| AWS Cognito            | User authentication                           |
| API Gateway            | REST API for dashboard interaction            |
| S3 + CloudFront        | Hosting the frontend app                      |
| Kinesis Video Streams  | Handle real-time video feed                   |
| SNS (optional)         | Alert notifications via email/SMS            |

---

## 🗃️ Database Design (DynamoDB)

**Table Name**: `CoopTelemetry`

| Attribute     | Type      | Description                          |
|---------------|-----------|--------------------------------------|
| deviceId      | String (PK) | Unique coop system ID                |
| timestamp     | String (SK) | ISO8601 time of data                 |
| temperature   | Number    | °C from DHT22                        |
| humidity      | Number    | % RH from DHT22                      |
| light         | Number    | Lux from BH1750 or LDR               |
| doorStatus    | String    | open, closed, error                  |

- TTL enabled on `timestamp` for automatic record expiration (e.g., 30 days)

---

## 🌐 Dashboard Features

- Built with **React + Tailwind**
- Displays live data: temp, humidity, light, door status
- Manual door control
- View embedded video stream (via HLS)
- Secure login via AWS Cognito

---

## 📈 Example Payload

```json
{
  "deviceId": "coop-001",
  "timestamp": "2025-04-22T18:30:00Z",
  "temperature": 23.5,
  "humidity": 60.2,
  "light": 300.4,
  "doorStatus": "open"
}
```

---

## 🔄 Scalability Considerations

- **Multi-Coop Support**: Unique `deviceId` for each coop
- **Multi-User Support**: Scoped access through Cognito user pools
- **DynamoDB TTL**: Control data retention and cost
- **Kinesis Video**: Auto-scales but cost-sensitive

---

## ⚖️ Alternatives & Comparison

| Feature                | Smart Coop (AWS) | DIY Local | Commercial Units |
|------------------------|------------------|-----------|------------------|
| Remote Monitoring      | ✅                | ❌        | ✅                |
| Cloud Logging          | ✅                | ❌        | ❌                |
| Live Video Streaming   | ✅                | ❌        | ❌                |
| Auto Door Control      | ✅                | ✅        | ✅                |
| Dashboard Access       | ✅                | ❌        | ❌                |
| Customizable           | ✅                | ✅        | ❌                |
| Cost                   | Medium           | Low       | High             |

---

## 🗓️ Development Timeline (3 Months)

### Month 1: Foundation
- Order hardware components
- Setup AWS CDK stack: IoT, Lambda, DynamoDB
- Send sample sensor data from Raspberry Pi to AWS

### Month 2: Control + Streaming
- Wire motor + limit switches to Pi
- Implement door control and monitoring logic
- Connect Pi camera and stream to Kinesis
- Begin dashboard development (sensor visualization + control)

### Month 3: Finalize & Deploy
- Build HLS viewer in React for live stream
- Add SNS-based alerting
- Deploy dashboard to S3 + CloudFront
- Final testing and documentation

---

## 📁 Repository Structure

```
smart-chicken-coop/
├── pi-code/            # Pi sensor + control + video scripts
├── hardware/           # Wiring diagrams & parts list
├── cloud/              # CDK templates and Lambdas
├── dashboard/          # React-based frontend
├── docs/               # Design and planning
└── README.md
```

---

## 🔮 Future Enhancements

- Motion-triggered recording
- OTA updates for Pi
- Custom schedules via dashboard
- Mobile app
- Solar + battery telemetry

