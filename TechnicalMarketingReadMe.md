# Technical Marketing Summary — Egbert

## One-Line Positioning

A browser-based Aikido roll-analysis tool that uses smartphone sensors to measure motion smoothness, bundled with an interactive author biography mind map and a Tetris game — all deployed on GitHub Pages.

## Target Users / Personas

- **Aikido practitioners** who want to measure and improve the smoothness of their rolls using their phone's sensors.
- **Aikido coaches / instructors** using multi-device mode to monitor multiple students simultaneously.
- **Richard Moon readers / fans** exploring the author's biography via an interactive mind map.
- **Book launch team** referencing the Quantum Aikido launch plan and blog content strategy.

## Key Features (Grounded in Code)

- **Roll Smoothness Analyzer** (`index.html`, `js/`) — records accelerometer data via the `devicemotion` API for 10-second intervals and visualizes G-force magnitude using Chart.js.
- **Five operating modes** — Single (one device), Multi (networked devices), Coach, Logs, and Sensors — selectable via a mode-selector nav.
- **Sensor monitoring** (`js/sensors.js`) — real-time graphs for accelerometer, gyroscope, orientation, and derived data with high-DPI canvas rendering.
- **Motion analysis** (`js/analysis.js`) — records acceleration data, auto-stops after 10 seconds, requests iOS motion permissions.
- **Persistent logging** (`js/utils.js`) — system logs saved to `localStorage` (last 100 entries) with timestamps and categories.
- **Interactive biography mind map** (`RichardMoon.html`) — draggable, visual mind map of Richard Moon's life and teachers with gradient background.
- **Tetris game** (`tetris-complete.html`, `tetris.js`) — full Tetris implementation with 7 piece types, mobile touch support, and professional UI.
- **Book launch planning docs** — `quantum_aikido_launch_plan_v4.md` (4-track strategy, Oct 2025–Mar 2026), `BookLaunchQA` (4-week blog series), `egbert_quantum_aikido_version.md` (children's Aikido story).
- **CI/CD** — GitHub Actions workflow auto-deploys to GitHub Pages on push to main.

## Technical Differentiators

- **No app store required** — the roll analyzer runs entirely in a mobile browser using the `DeviceMotionEvent` API; no native app installation.
- **Modular JS architecture** — clean separation into `main.js`, `analysis.js`, `sensors.js`, `ui.js`, `utils.js`.
- **Chart.js visualization** — professional line-chart rendering of G-force data with gradient fills.
- **High-DPI sensor graphs** — custom canvas rendering with `devicePixelRatio` scaling for crisp sensor visualizations.
- **Multi-device support** — designed for networked multi-sensor scenarios (coach monitoring multiple students).
- **Zero-backend** — all data stays on-device; logs persist via `localStorage`.

## Use Cases

- Measuring Aikido roll smoothness during solo practice.
- Coaching multiple students with real-time sensor feedback.
- Exploring Richard Moon's Aikido lineage and biography interactively.
- Playing Tetris (mobile-friendly) as a break or demo.
- Planning and referencing the Quantum Aikido book launch strategy.

## Benefits / Value Proposition

- **Accessible** — works on any smartphone browser with sensors; no app download.
- **Data-driven practice** — objective motion measurement replaces subjective "felt smooth" assessment.
- **Free and open** — MIT-licensed, hosted on GitHub Pages at no cost.
- **Auto-deployed** — push to main triggers GitHub Pages deployment automatically.
- **Multi-purpose** — combines training tool, educational content, and entertainment in one site.

## Tech Stack

- **Frontend**: HTML5, CSS3 (responsive), vanilla JavaScript (ES5/ES6)
- **Libraries**: Chart.js (acceleration charts), Font Awesome 6.4 (icons), Google Fonts (Inter)
- **APIs**: DeviceMotionEvent, localStorage, Canvas 2D Context
- **Deployment**: GitHub Pages via GitHub Actions
- **Content**: Markdown planning documents
- **License**: MIT

## Known Limitations

- **iOS permission gate** — Safari requires explicit `DeviceMotionEvent.requestPermission()` user gesture; sensor access may be blocked.
- **Desktop limitation** — the roll analyzer requires accelerometer/gyroscope sensors; desktop browsers without sensors cannot record motion data.
- **10-second recording cap** — recording auto-stops after 10 seconds; longer sessions require multiple recordings.
- **No data export** — recorded motion data is visualized but not exportable to file.
- **Multi-device mode** — the networking implementation for multi/coach modes is not fully evident in the current code.
- **No automated tests** — the project has no test suite.
