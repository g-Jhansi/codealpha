# AI-Based Real-Time Object Detection & Multi-Object Tracking System

[![FastAPI](https://img.shields.io/badge/FastAPI-0.110+-009688.svg?style=flat&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![YOLOv8](https://img.shields.io/badge/YOLOv8-Ultralytics-00599C.svg?style=flat&logo=opencv&logoColor=white)](https://github.com/ultralytics/ultralytics)
[![ByteTrack](https://img.shields.io/badge/Tracker-ByteTrack-FF6F00.svg?style=flat)](https://github.com/ifzhang/ByteTrack)
[![React](https://img.shields.io/badge/React-18%2F19-61DAFB.svg?style=flat&logo=react&logoColor=black)](https://react.dev)
[![Vite](https://img.shields.io/badge/Vite-8.0+-646CFF.svg?style=flat&logo=vite&logoColor=white)](https://vitejs.dev)

A modern, commercial-grade Computer Vision and SaaS Analytics application for real-time multi-object detection, persistent tracking identifier assignment, spatio-temporal trajectory reconstruction, and automated audit reporting.

Built strictly according to **PRD.md**, **Gemini.md**, and **BrandGuidelines.md** as a **Final Year Engineering Capstone Project**.

---

## 📌 Key Capabilities & Features

1. **AI Object Detection**:
   - Built on **YOLOv8** deep learning architecture (CSPDarknet backbone + PANet neck + decoupled anchor-free head).
   - Optimized for target classes: `person`, `car`, `bicycle`, `motorcycle`, `bus`, `truck`, `dog`, `cat`, `laptop`, `cell phone`, `chair`, `bottle` (and full 80 COCO classes).
   - Real-time bounding box annotations with corner accents, color-coded class pills, and confidence percentages.

2. **Multi-Object Tracking (MOT)**:
   - Built with **ByteTrack** multi-object tracking algorithm.
   - Low-score and high-score detection association using **Kalman Filters** and **Hungarian Matching**.
   - Maintains **persistent tracking IDs** across frames without ID-switching during partial occlusion or crossing trajectories.
   - Traces **motion paths** (breadcrumbs/trajectory polylines) for each tracked object.

3. **Live Camera Module**:
   - Bidirectional **WebSocket streaming** (`/api/live/ws-stream`) transferring client webcam frames with low latency (<50ms).
   - Real-time HUD showing instantaneous **FPS**, **inference latency (ms)**, and **active object counts**.
   - One-click **Snapshot Capture** that saves annotated detection frames directly to detection history.

4. **Video Upload & Offline Processing**:
   - Drag-and-drop uploader supporting `.mp4`, `.avi`, and `.mov`.
   - Asynchronous background worker (`cv2.VideoCapture` + `cv2.VideoWriter`) annotating frames and persisting all tracks.
   - Real-time percentage progress bar polling.
   - HTML5 video player with toggle between **AI-Annotated** and **Raw Source** video.

5. **Detection History & Telemetry Search**:
   - Searchable, filterable, sortable, and paginated data table.
   - Filter by date range, object class, minimum confidence, or tracking ID.
   - Interactive modal preview for captured snapshots.

6. **Analytics & Data Visualizations**:
   - Interactive **Chart.js** visualizations.
   - Daily, weekly, and monthly detection frequency timelines.
   - Class breakdown doughnut and ranking bar charts.
   - Detection confidence distribution histogram.

7. **Regulatory Compliance & Reporting**:
   - **Executive PDF Reports** compiled dynamically using **ReportLab** with branded headers, KPI metric cards, and tabular logs.
   - **CSV Export** for spreadsheet auditing and scientific evaluation.

8. **Security & Role-Based Access Control (RBAC)**:
   - OAuth2 Password Bearer authentication issuing signed **JSON Web Tokens (JWT)**.
   - Passwords hashed with **bcrypt** salt rounds.
   - Roles: `admin` (System Administrator) and `user` (Standard Operator).

---

## 🎨 UI & Brand Guidelines Compliance

The user interface strictly adheres to `Brand Guidelines`:
- **Color Palette**:
  - Primary: `#2563EB` (Blue)
  - Secondary: `#0F172A` (Navy Slate)
  - Success: `#22C55E` (Green)
  - Warning: `#F59E0B` (Amber)
  - Danger: `#EF4444` (Red)
  - Background: `#F8FAFC`
  - Surface: `#FFFFFF`
  - Border: `#E5E7EB`
- **Typography**:
  - Headings: **Poppins**
  - Body: **Inter**
  - Monospace / Telemetry: **JetBrains Mono**
- **Design Elements**:
  - Light glassmorphism navigation with backdrop blur (`blur(12px)`).
  - Consistent **12px rounded borders** (`--radius-lg: 12px`).
  - Soft SaaS shadows and subtle hover micro-animations.
  - Exclusively **Lucide Icons** (`lucide-react`).

---

## 🏛️ System Architecture

```
                                 [ Browser Client ]
                          (React 18 + Vite + Chart.js)
                                  /            \
                       HTTP / REST              WebSocket (JPEG Frames)
                         (JWT Auth)                   \
                            /                          \
            +--------------v----------------------------v---------------+
            |                    FastAPI Backend Server                 |
            |                                                           |
            |  +-------------------+  +-------------------------------+ |
            |  |  Routers & Auth   |  |     Live WebSocket Stream     | |
            |  +---------+---------+  +---------------+---------------+ |
            |            |                            |                 |
            |  +---------v----------------------------v---------------+ |
            |  |             AIEngine (YOLOv8 + ByteTrack)            | |
            |  |  - Feature Extraction (CSPDarknet + PANet)           | |
            |  |  - Multi-Object Tracking (Kalman Filter + Lap/MOT)   | |
            |  |  - Trajectory Spatio-temporal Calculation            | |
            |  +-----------------------+------------------------------+ |
            |                          |                                |
            |  +-----------------------v------------------------------+ |
            |  |               SQLAlchemy ORM Data Layer              | |
            |  |  (MySQL 8.0+ / Zero-Config SQLite Auto-Fallback)     | |
            |  +------------------------------------------------------+ |
            +-----------------------------------------------------------+
```

---

## 🗄️ Database Schema

The database follows a normalized relational structure:
- **`users`**: User identity, role (`admin`/`user`), credentials, timestamps.
- **`videos`**: Uploaded video files, durations, resolutions, FPS, processing status (`uploaded`, `processing`, `completed`, `failed`).
- **`tracking_sessions`**: Live camera sessions and video processing runs.
- **`tracking_objects`**: Unique persistent entities assigned by ByteTrack, duration visible, and trajectory coordinates (`JSON`).
- **`detections`**: Frame-level bounding box coordinates (`bbox_x1, y1, x2, y2`), confidence score, timestamp, snapshot path.
- **`reports`**: Generated PDF and CSV audit reports.
- **`activity_logs`**: System audit trail logging all user actions.

*(Full MySQL DDL script is located in [database/schema.sql](database/schema.sql))*.

---

## 🚀 Getting Started & Installation

### Prerequisites
- **Python 3.10+** (Tested on Python 3.13)
- **Node.js 18+** & **npm**
- Modern Web Browser (Chrome, Edge, Firefox)

### 1. Quick Start (Windows Master Launcher)
Simply double-click:
```bat
start_system.bat
```
This launcher starts both the FastAPI backend and React frontend dev server concurrently.

---

### 2. Manual Setup

#### Step 2.1: Backend Setup
```bash
# 1. Install backend dependencies
python -m pip install -r backend/requirements.txt

# 2. Run automated verification tests
python backend/tests/test_system.py

# 3. Start the FastAPI server
python -m uvicorn backend.app.main:app --host 0.0.0.0 --port 8000 --reload
```
*The backend will be running at **http://localhost:8000**.*
*Interactive Swagger API documentation is available at **http://localhost:8000/docs**.*

#### Step 2.2: Frontend Setup
```bash
# 1. Navigate to the frontend folder
cd frontend

# 2. Install frontend packages
npm install

# 3. Start the development server
npm run dev
```
*The frontend dashboard will be running at **http://localhost:5173**.*

---

## 🔑 Default Credentials

The system seeds default accounts automatically on first startup:

| Role | Username | Password | Access Level |
| :--- | :--- | :--- | :--- |
| **System Administrator** | `admin` | `Admin@123` | Full access to all videos, users, logs, and AI thresholds |
| **Standard Operator** | `user` | `User@123` | Personal live camera streaming, video uploads, and reports |

*(You can also use the one-click demo login buttons on the login page)*.

---

## 🧪 Testing & Verification

Run the automated test suite verifying all 5 core subsystems:
```bash
python backend/tests/test_system.py
```
**Test Coverage**:
1. Relational Database & Table Auto-Creation
2. Bcrypt Password Hashing & JWT Signature / Decoding
3. YOLOv8 Inference + ByteTrack Tracking Latency & Trajectory Calculation
4. Multi-Object Session and Detection Logging
5. PDF and CSV Audit Report Generation

---

## 📁 Repository Structure

```
AI/
├── PRD.md                         # Product Requirement Document
├── Gemini.md                      # System Architecture & Academic Specification
├── Brand Guidelines               # Visual Design & Branding Tokens
├── README.md                      # Project Manual & Setup Guide
├── start_system.bat               # Windows Concurrent Launcher
├── run_backend.bat                # Backend Runner
├── run_frontend.bat               # Frontend Runner
│
├── database/
│   └── schema.sql                 # Normalized MySQL Database Schema Script
│
├── backend/
│   ├── requirements.txt           # Python Dependencies
│   ├── tests/
│   │   └── test_system.py         # Automated Subsystem Test Suite
│   ├── scripts/
│   │   └── generate_sample_video.py  # Sample MP4 Video Generator
│   └── app/
│       ├── main.py                # FastAPI Application & Startup Seeder
│       ├── config.py              # Environment & Hyperparameter Settings
│       ├── database.py            # SQLAlchemy Connection Engine
│       ├── models/                # Database Relational Entities
│       ├── schemas/               # Pydantic Validation Schemas
│       ├── services/
│       │   ├── ai_engine.py       # YOLOv8 & ByteTrack Multi-Object Tracking Engine
│       │   ├── video_processor.py # Asynchronous Video Frame Processor
│       │   ├── report_service.py  # ReportLab PDF & CSV Generators
│       │   └── auth_service.py    # JWT Tokens & Bcrypt Password Security
│       └── routers/               # Modular REST API Endpoints
│
└── frontend/
    ├── index.html                 # Brand Typography (Poppins, Inter, JetBrains Mono)
    ├── package.json               # Frontend Dependencies
    ├── vite.config.js             # Vite Configuration
    └── src/
        ├── main.jsx               # React DOM Entry
        ├── App.jsx                # Routing & Authentication Guards
        ├── styles/
        │   └── index.css          # Design Tokens & Master Stylesheet
        ├── context/
        │   ├── AuthContext.jsx    # User Session & JWT State
        │   └── ToastContext.jsx   # Feedback Toast Notifications
        ├── services/
        │   └── api.js             # Axios Interceptors & WebSocket URLs
        ├── components/
        │   └── layout/
        │       ├── Sidebar.jsx    # Brand Guidelines Left Navigation
        │       ├── Navbar.jsx     # Glassmorphic Header & Status Indicator
        │       └── Layout.jsx     # App Container
        └── pages/
            ├── LoginPage.jsx      # Authentication & Demo Fill
            ├── RegisterPage.jsx   # Operator Account Creation
            ├── ForgotPasswordPage.jsx # Password Reset
            ├── DashboardPage.jsx  # KPI Cards, Recent Feeds, & Charts
            ├── LiveCameraPage.jsx # Live Stream, HUD, & Snapshot Capture
            ├── VideoManagementPage.jsx # Upload, Video Player, & AI Processing
            ├── DetectionHistoryPage.jsx # Filter, Search, & Pagination Table
            ├── AnalyticsPage.jsx  # Time-Series Trends & Histograms
            ├── ReportsPage.jsx    # PDF / CSV Audit Report Downloader
            └── SettingsPage.jsx   # AI Hyperparameters & System Config


FAQMate Web App

A friendly and professional FAQ chatbot for answering user questions based on a predefined knowledge base.

## Features
- Minimalist and clean UI following brand guidelines.
- TF-IDF and Cosine Similarity for FAQ matching.
- Built with Flask (Python).

## Setup
1. Create a virtual environment:
   ```bash
   python -m venv venv
   venv\Scripts\activate
   ```
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Run the application:
   ```bash
   python app.py
   ```
4. Open your browser and go to `http://localhost:5000`.

## Architecture
- `app.py`: Flask Web Server
- `src/`: Chatbot logic and similarity computation
- `data/`: Knowledge base storage
- `templates/` & `static/`: Frontend UI files
