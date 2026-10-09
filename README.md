# factory-safety-monitoring
# Real-Time Factory Safety Monitoring System (AI Prototype)

An edge-deployable, real-time computer vision system that monitors industrial worker PPE compliance (hard hats, hi-vis vests, protective gloves, safety goggles) by zone, detects early fire/smoke hazards, and distributes live alerts across Webhook, Slack, Teams, MQTT, and interactive dashboard interfaces.

---

## 🛠️ Architecture & Tech Stack
         [RTSP / Camera Streams / Video Simulation]
                              │
                              ▼
                 [edge_inference.py Pipeline]
     ┌────────────────────────┴────────────────────────┐
     ▼                                                 ▼
          ▼                                                 ▼

  

[YOLO PPE Model]                              [YOLO Fire/Smoke Model]
(Person, Helmet, Vest,                           (Early Fire, Smoke)
Gloves, Goggles)                                        │
│                                                 │
└────────────────────────┬────────────────────────┘
▼
[Compliance Engine (IoU)]
(Zone PPE Rules & Containment)
│
▼
[Temporal Smoother (M-of-N)]
│
┌────────────────┴────────────────┐
▼                                 ▼
[Evidence Snapshot (JPG)]          [FastAPI Backend Server]
(/alerts, /cameras, /ws)
│
┌───────────────────────────────┼──────────────────────────────┐
▼                               ▼                              ▼
[Streamlit UI]                 [Webhook / PLC]                 [MQTT / Sirens]



  
- **Vision Models**: Ultralytics YOLOv8-nano / YOLOv11-nano (PPE & Fire/Smoke Detection, Man-Down / Worker Fall Detection)
- **Inference Runtime**: ONNX Runtime, TensorRT (INT8), OpenVINO
- **Backend**: FastAPI, WebSockets, SQLAlchemy, SQLite, Pydantic
- **Dashboard**: Streamlit (live camera grid, 2D floor hazard topography, alert triage queue)
- **Alert Dispatching**: Multi-sink router supporting REST Webhook, Slack (Block Kit), Microsoft Teams (Adaptive Cards), MQTT (Sirens/PLCs), and SMTP Email
- **Deployment**: Docker, Docker Compose, NVIDIA Jetson Nano / Orin, Raspberry Pi 5 + Coral Edge TPU

---

## ⚙️ Quick Start

### 1. Prerequisites
- Python 3.10 – 3.13
- Git

### 2. Environment Setup
```bash
# Clone and enter workspace
git clone https://github.com/biswojitpradhan2007-png/factory-safety-monitoring.git
cd factory-safety-monitoring

# Create and activate virtual environment
python -m venv .venv
source .venv/bin/activate       # On Linux/macOS
.venv\Scripts\activate          # On Windows

# Install pinned dependencies
pip install -r requirements.txt
docker-compose up --build
python -m uvicorn src.server.app:app --host 127.0.0.1 --port 8000
