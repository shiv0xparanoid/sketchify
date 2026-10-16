<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f0f0f,50:3a3a3a,100:8e8e8e&height=200&section=header&text=Sketchify&fontColor=f5f1e8&fontSize=64&fontAlignY=38&desc=turn%20any%20photo%20into%20a%20pencil%20sketch&descAlignY=60&descSize=17" width="100%" />

<br/>

![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![Tailwind](https://img.shields.io/badge/Tailwind-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-000000?style=for-the-badge&logo=flask&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white)
![OpenCV](https://img.shields.io/badge/OpenCV-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white)
![ONNX](https://img.shields.io/badge/ONNX%20Runtime-005CED?style=for-the-badge&logo=onnx&logoColor=white)
![Render](https://img.shields.io/badge/Render-46E3B7?style=for-the-badge&logo=render&logoColor=black)

**Upload a photo. Get back a hand-drawn look.**
An AI-powered image-to-pencil-sketch web app, built on U²-Net.

[![GitDiagram](https://img.shields.io/badge/Explore%20on-GitDiagram-1a1a1a?style=flat-square)](https://gitdiagram.com/shiv0xparanoid/sketchify)

[About](#-about) · [Workflow](#-workflow) · [Tech Stack](#-tech-stack) · [Structure](#-project-structure) · [Run Locally](#-run-locally) · [Deploy](#-deploy)

</div>

<br/>

## ✦ About

Sketchify takes an ordinary photo and redraws it as a pencil sketch. A React frontend handles the upload and preview, and a Python backend runs the deep-learning pipeline built around **U²-Net**, then returns the finished sketch to the browser.

<!--
Add a before/after pair here once you have one:

| Photo | Sketch |
|---|---|
| <img src="test.jpg" width="320" /> | <img src="docs/assets/sketch-demo.png" width="320" /> |
-->

<br/>

## ✦ Workflow

> 🔍 **Interactive version:** open this repo on **[GitDiagram](https://gitdiagram.com/shiv0xparanoid/sketchify)** for the auto-generated, clickable architecture view. (Replace `github.com` with `gitdiagram.com` in any repo URL.) The diagrams below are the curated versions.

### 1 · System architecture

```mermaid
flowchart LR
    subgraph CLIENT["🖥️  client · React + Tailwind"]
        direction TB
        UP["Image Upload"]
        PV["Live Preview"]
        DL["Download Sketch"]
    end

    subgraph SERVER["⚙️  server · Python"]
        direction TB
        API["Flask API<br/>+ CORS"]
        ML["U²-Net<br/>PyTorch · ONNX Runtime"]
        CV["OpenCV / scikit-image<br/>pencil rendering"]
        API --> ML --> CV
    end

    subgraph STORE["📁  Outputs/"]
        OUT["generated sketches"]
    end

    UP -->|"POST image"| API
    CV --> OUT
    OUT -->|"sketch"| PV
    PV --> DL

    HOST[("☁️ Render<br/>web service")] -.hosts.-> SERVER

    classDef c fill:#1c1c1c,stroke:#8e8e8e,color:#f5f1e8
    classDef s fill:#2b2b2b,stroke:#d6d0c2,color:#f5f1e8
    classDef o fill:#3a3a3a,stroke:#f5f1e8,color:#f5f1e8
    class UP,PV,DL c
    class API,ML,CV s
    class OUT,HOST o
```

### 2 · Photo → sketch pipeline

```mermaid
flowchart LR
    A["📷 Photo"] --> B["Pre-process<br/>resize · normalize"]
    B --> C["U²-Net<br/>inference"]
    C --> D["Saliency / edge map"]
    D --> E["OpenCV<br/>pencil shading"]
    E --> F["✏️ Sketch"]

    classDef a fill:#1c1c1c,stroke:#8e8e8e,color:#f5f1e8
    classDef b fill:#2b2b2b,stroke:#d6d0c2,color:#f5f1e8
    class A,F a
    class B,C,D,E b
```

### 3 · Request flow

```mermaid
sequenceDiagram
    autonumber
    actor U as User
    participant W as React Client
    participant S as Flask Server
    participant M as U²-Net + OpenCV

    U->>W: choose photo
    W->>S: POST /upload (multipart)
    S->>M: run model
    M-->>S: sketch image
    S-->>W: sketch (saved to Outputs/)
    W-->>U: preview and download
```

<br/>

## ✦ Tech Stack

| Layer | Tools |
|---|---|
| **Frontend** | React (Create React App) · Tailwind CSS · PostCSS |
| **Backend** | Python · Flask · Flask-CORS |
| **Model** | U²-Net · PyTorch · ONNX Runtime |
| **Image processing** | OpenCV · scikit-image · Pillow · rembg |
| **Hosting** | Render |

<br/>

## ✦ Project Structure

```
sketchify/
├── client/            # React frontend
├── src/ · public/     # React app source and static assets
├── server/            # Python backend API
├── U-2-Net/           # U²-Net model code
├── Outputs/           # generated sketches
├── static/
├── test_upload.py     # quick upload test against the API
├── requirements.txt   # Python dependencies
├── render.yaml        # Render deployment config
└── package.json
```

<br/>

## ✦ Run Locally

> **Needs:** Node 18+, Python 3.10+. A GPU is optional, since the model also runs on CPU.

```bash
git clone https://github.com/shiv0xparanoid/sketchify.git
cd sketchify
```

**Backend**

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
cd server && python app.py
```

**Frontend** (new terminal)

```bash
npm install
npm start                        # http://localhost:3000
```

The frontend reads the API address from `.env.development`. Point it at your local server when developing.

> 💡 U²-Net needs its pretrained weights. If they aren't in the repo, download them into `U-2-Net/` before the first run.

**Test the API without the UI**

```bash
python test_upload.py
```

<br/>

## ✦ Deploy

The backend is set up as a **Render** web service via `render.yaml` (build: `pip install -r requirements.txt`). Set the production API URL in `.env.production` so the frontend talks to the deployed backend.

<br/>

## ✦ Roadmap

- [ ] Sketch style presets (soft pencil, charcoal, ink line)
- [ ] Adjustable stroke and shading intensity
- [ ] Batch upload
- [ ] Before/after slider in the preview

<br/>

<div align="center">

Built by **[Shivang](https://github.com/shiv0xparanoid)**

<sub>If Sketchify turned your selfie into art, drop a ⭐</sub>

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:8e8e8e,50:3a3a3a,100:0f0f0f&height=120&section=footer" width="100%" />

</div>
