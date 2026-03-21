# GPS Speedometer

A single-file, zero-dependency GPS speedometer web app built for mobile browsers. Open `speed.html` on any phone — no install, no server required.

---

## Features

### Two Display Themes
Switch with the 🏎 button (top-right corner).

| Gauge Mode | F1 LED Mode |
|---|---|
| Analog needle dial with tick marks | 15-cell LED bar (green → amber → red) |
| Large split integer/decimal readout | Giant zero-padded 3-digit display |
| Frosted glass stat cards | Compact grid: Max / Distance / Avg |

### Real-time Stats
- **Current speed** — integer + decimal, unit label below
- **Today Max / Today Min** — persisted per day
- **All-time Max** — scanned across all saved days
- **Trip distance** — integrated from GPS over time
- **Average speed** — toggle between Moving Avg and Total Avg

### GPS Drift Defence (3-layer)
1. **Accelerometer gate** — if the device is stationary (accel < 0.3 m/s²), speed is forced to zero regardless of GPS
2. **Spike rejection** — GPS jumps implying > 8 m/s² acceleration that the accelerometer doesn't support are discarded
3. **Sustained motion gate** — speed is only shown and recorded after 5 continuous seconds of movement; brief GPS noise blips are ignored

### Other
- **Daily history drawer** — swipe up from the bottom, shows last 7 days
- **Screen wake lock** — prevents screen sleep while navigating
- **Fullscreen button** — hides browser chrome for a clean dashboard view
- **Landscape layout** — stats column moves to the side, nothing is covered by the history bar

---

## Usage

1. Open `speed.html` in a mobile browser (Chrome / Safari)
2. Grant location permission when prompted
3. Start moving — speed confirms after 5 seconds of sustained motion
4. Tap 🏎 to switch to F1 LED mode
5. Tap the history bar at the bottom to see past days

> **iOS note:** Motion sensor permission is requested automatically on first GPS start (iOS 13+).

---

## Configuration

At the top of the `<script>` block in `speed.html`:

```js
const DISPLAY_UNITS = UNITS.kmh;   // change to UNITS.mph for miles
const IGNORE_SPEED_BELOW = 0.5;    // km/h noise floor
const MOTION_CONFIRM_MS = 5000;    // ms of sustained motion before speed is shown
const ACCEL_STILL_THRESHOLD = 0.3; // m/s² — below this = stationary
const MAX_ACCEL_FROM_GPS = 8;      // m/s² — GPS spike rejection threshold
```

---

## Data Storage

All data is saved in `localStorage` under keys like `speedometer.2026-03-21`. No data leaves the device.

To clear all history: open DevTools → Application → Local Storage → delete `speedometer.*` keys.

---

## Browser Support

| Browser | GPS | Motion Sensor |
|---|---|---|
| Chrome Android | ✅ | ✅ |
| Safari iOS | ✅ | ✅ (permission prompt) |
| Firefox Android | ✅ | ✅ |
| Desktop browsers | ⚠️ (no GPS hardware) | ❌ |
