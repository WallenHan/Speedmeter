# Speedmeter - Project Guidelines for AI Agents

This is an essential reference document for AI agents assisting in the maintenance, iteration, and refactoring of this project. When tasked with modifying this project, you (the AI Agent) MUST read and adhere to the principles outlined here first.

## 1. Project Overview
- **Name**: GPS Speedometer
- **Type**: Mobile-first Web Application (Single Page Application)
- **Core Features**: Real-time speed rendering, max/average speed tracking, and distance calculation utilizing the device's GPS and Accelerometer (IMU). Features two UI themes: standard Analog and an F1-style LED dashboard.

## 2. Tech Stack
- **Languages**: Vanilla HTML5, CSS3, and ES6 JavaScript. (No build step, pure native approach for ultra-fast load times).
- **CSS Framework**: Tailwind CSS is injected globally via CDN for utility-first layout. Custom raw CSS (`<style>`) is strictly utilized for specialized, locally scoped graphics (glow effects, gauge rendering, keyframes).
- **Iconography**: Inline SVG vector icons (Lucide style) MUST be utilized instead of Emojis.
- **Storage**: `localStorage` is used to cache historic data locally, persistently recording the "Max Speed and Distance" of the day.

## 3. Core Algorithm Highlights (CRITICAL)
This app heavily incorporates a sophisticated **Sensor Fusion Strategy** inside `speed.html` to solve industry pain points like *GPS drifting at traffic lights* or *signal reflections between skyscrapers*. **DO NOT randomly alter these parameters**:

1. **Gravity Stripping Filter**
   Relies on `DeviceMotionEvent`. For cross-browser compatibility and older devices, gracefully falls back to `accelerationIncludingGravity`. A **Low-Pass Filter** is applied to estimate and isolate the gravity vector. A **High-Pass Filter** subsequently removes it to extract the pure physical linear acceleration magnitude (`accelMag`).
2. **Stationary Drop-off Validation**
   If `accelMag < 0.3 m/s²` (the device is physically stationary), forcefully overwrite the GPS speed output to `0`. This prevents artificial distance accumulation from GPS signal drift while waiting at red lights.
3. **Implied Acceleration Rejection (Spike Guard)**
   Utilizes delta-time to calculate an "Implied Accel" from the change in GPS coordinates. If a massive jump occurs (e.g., >8m/s²) BUT the accelerometer registers no corresponding inertial thrust, the spike is branded anomalous and dropped.
4. **Accuracy Gate**
   If `pos.coords.accuracy > 40` meters (indicative of weak signal environments like tunnel exits), silently reject the data coordinate.

*CRUCIAL REMINDER: No artificial hard-coded delay (like 5-second locks) should be used. The speedometer UI must instantly reflect physical acceleration during a vehicle launch.*

## 4. UI & Interaction Design Principles
- **Mobile-first View**: In Portrait mode, prioritize the Gauge view visually by placing it exclusively at the top. The statistics should be pushed below using CSS Grid. In Landscape mode, arrange components side-by-side.
- **Notch / Safe-Area Insets**: Always employ `env(safe-area-inset-*)` dynamically scaling CSS paddings to avoid screen notches and corner clipping.
- **Monofile Portability**: Keep ALL templates, styles, and logic fully encapsulated inside `speed.html` unless explicitly requested otherwise. This boosts transportability as a single accessible local-tool.

## 5. Agent Verification Checklist
Before submitting any code changes, rigorously verify:
- [ ] Have my UI changes preserved the responsive layout across Mobile dimensions (Portrait/Landscape)?
- [ ] Do any new animations cause repaints or performance drops? (Strictly prefer DOM updates via CSS `transform` / `opacity`).
- [ ] Give close attention to the Anti-Drift Sensor filters. Has the core logic been disrupted?
- [ ] Are iOS proprietary APIs (e.g., `DeviceMotionEvent.requestPermission` and WakeLock policies) handled robustly with silent fallbacks?
- [ ] Does the `Toggle Fullscreen` experience remain seamless?
