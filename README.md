# CampusIQ — Smart Campus Resource Optimization System

CampusIQ is an AI + IoT platform designed to monitor campus electricity and water utilization, predict demand using time-series forecasting, detect leaks/spikes in real-time using Isolation Forest models, and provide actionable recommendations for campus administrators.

## 🏗 System Architecture
- **Sensors / Edge**: ESP32 with PIR motion sensor, PZEM-004T energy sensor, YF-S201 flow sensor
- **Backend**: Python FastAPI with WebSocket real-time telemetry streaming
- **ML / Analytics**: Isolation Forest (Anomaly Detection), Prophet / XGBoost (Demand Prediction)[cite: 1]
- **Frontend**: React.js with Tailwind CSS & WebSockets[cite: 1]

## 🚀 Quick Start (Backend)

```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
