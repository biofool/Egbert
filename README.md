# Egbert

A GitHub Pages site hosting multiple web apps and content related to Richard Moon's *Quantum Aikido*: a **Roll Smoothness Analyzer** (device-sensor motion analysis tool), an interactive **Richard Moon biography mind map**, a **Tetris game**, and book launch planning documents.

## Overview

Egbert is a multi-purpose static site deployed via GitHub Pages. Its primary application is the **Roll Smoothness Analyzer** ("Slow Your Roll: Helper!") — a browser-based tool that uses device accelerometer/gyroscope sensors (`devicemotion` API) to record and analyze motion data for Aikido roll practice. The site also includes an interactive mind map of Richard Moon's biography, a complete Tetris game, and markdown planning documents for the *Quantum Aikido* book launch.

## Prerequisites

- A modern web browser with JavaScript enabled.
- **For the Roll Analyzer**: a mobile device with accelerometer/gyroscope sensors and a browser supporting the `DeviceMotionEvent` API (iOS Safari requires explicit permission via `DeviceMotionEvent.requestPermission()`).
- Internet access for CDN resources (Font Awesome, Google Fonts, Chart.js).

## Setup

```bash
git clone https://github.com/biofool/Egbert.git
cd Egbert
```

No build step required. Open `index.html` in a browser, or serve locally:

```bash
python3 -m http.server 8000
# visit http://localhost:8000/
```

The site is automatically deployed to GitHub Pages via a GitHub Actions workflow (`.github/workflows/static.yml`).

## How to Run / Use

### Roll Smoothness Analyzer

1. Open `index.html` on a mobile device (sensors required).
2. Select a mode: **Single** (one device), **Multi** (networked devices), **Coach**, **Logs**, or **Sensors**.
3. In Single mode, grant motion permission and tap to start recording.
4. Perform your Aikido roll; the app records accelerometer data for 10 seconds.
5. View the G-force magnitude chart and analysis results.

### Richard Moon Biography Mind Map

Open `RichardMoon.html` in a browser — an interactive, draggable mind map of Richard Moon's life and teachers.

### Tetris

Open `tetris-complete.html` in a browser — a full Tetris game with mobile touch support (logic in `tetris.js`).

## Project Structure

```
Egbert/
├── index.html                  # Roll Smoothness Analyzer (main app)
├── RichardMoon.html            # Interactive biography mind map
├── tetris-complete.html        # Tetris game (Professional Edition)
├── tetris.js                   # Tetris utility functions library
├── js/
│   ├── main.js                 # App state, initialization, animation loop
│   ├── analysis.js             # Roll recording & motion analysis logic
│   ├── sensors.js              # Sensor management & graph rendering
│   ├── ui.js                   # Chart.js acceleration chart
│   └── utils.js                # Logging & utility functions
├── styles/
│   ├── main.css                # Core styles
│   ├── components.css          # Component styles
│   └── responsive.css          # Responsive layout
├── RichardShot1.jpg            # Author photo
├── egbert_quantum_aikido_version.md   # "When Egbert Met Stevie" children's Aikido story
├── quantum_aikido_launch_plan_v4.md   # Book launch strategy (Oct 2025 – Mar 2026)
├── BookLaunchQA                # 4-week blog post series outline
├── VirtualDojoScript1.md.txt   # Virtual dojo script
├── LICENSE                     # MIT
├── README.md                   # This file
└── .github/workflows/static.yml  # GitHub Pages deployment workflow
```

## Notes

- The Roll Smoothness Analyzer uses `localStorage` to persist system logs between sessions.
- Recording auto-stops after 10 seconds.
- The Tetris game includes mobile touch interface support.
- The site is deployed at `https://biofool.github.io/Egbert/` via GitHub Pages.
