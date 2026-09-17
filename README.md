# Heart Rate Monitor

A single-page web app that connects directly to a Bluetooth Low Energy (BLE) heart rate sensor from your phone's browser — no native app install required. Built for Chrome on Android, styled after modern fitness dashboards (Apple Fitness / Whoop / Garmin), and hosted for free on GitHub Pages.

Tested against a **Crane/Workzone chest strap (model AA5-CDBT-1)**, but works with any sensor implementing the standard Bluetooth **Heart Rate Service**.

---

## Features

- **Live BLE connection** using the standard Heart Rate Service (`0x180D`) and Heart Rate Measurement characteristic (`0x2A37`) — no proprietary protocol needed.
- **Quick Connect** — after the first pairing, reconnect to the same sensor without the device picker.
- **Auto-reconnect on drop** — if the sensor loses contact mid-session, the app retries automatically with backoff instead of requiring a manual reconnect.
- **Live dial + big BPM number** that recolors by current training zone, with a heart icon that beats in sync with your real heart rate.
- **Trend chart** with:
  - A moving average (window length configurable, in seconds), recalculated every second.
  - Raw individual readings shown faintly behind it.
  - Training-zone colored bands and a gradient fill under the line that shifts color as you cross zones.
  - Automatically widens to show the whole session once it runs past 2 minutes, instead of scrolling old data off-screen.
- **Training zones** computed from age (`220 − age`): Resting, Warm Up, Fat Burn, Cardio, Hardcore, Peak.
- **Heart Rate Variability & stress** — RMSSD (ms) computed from the sensor's beat-to-beat data when available, with a 0–100% stress estimate and Low/Moderate/High/Very High label.
- **Workout mode**:
  - Start/Finish a workout with a live timer and a live time-in-zone bar.
  - Optional **2-minute post-workout recovery measurement** (toggle on/off) — captures Start HR, End HR, and HR at 1-minute and 2-minute recovery marks, plus the recovery drop at each.
  - Full summary on finish: duration, avg/min/max BPM, recovery figures, and time spent in each zone.
- **Export to CSV** — measured readings (workout + recovery, tagged by phase) plus a summary block; falls back to the whole session's readings if no workout was recorded.
- **Keep screen awake** toggle (Wake Lock API) for uninterrupted workout tracking.
- **Battery saver display** — a near-black, low-power OLED-friendly view showing just the BPM number.
- **Installable / offline-capable** — has a web app manifest and icon set for "Add to Home Screen", plus a service worker that caches the app shell so it still opens without a live connection.
- **Session persistence** — your readings, workout progress, and settings are saved continuously and survive a page reload or crash (you'll just need to tap Quick Connect again to resume live data).

---

## Getting started

This is a static site — no build step, no server-side code.

1. Host the files on **GitHub Pages** (or any static host):
   - Create a public GitHub repository.
   - Upload all the files below, keeping the folder structure intact.
   - In the repo, go to **Settings → Pages**, set the source to your `main` branch and `/ (root)`, and save.
   - Your app will be live at `https://<your-username>.github.io/<repo-name>/heart-rate-monitor.html`.
2. Open that link in **Chrome on Android** (Web Bluetooth is not available in Safari on iOS, or in most desktop browsers without flags).
3. Tap **Connect**, pick your sensor from the browser's device list, and readings will start streaming in.
4. Optional: tap **Add to Home Screen** in Chrome's menu to install it like an app.

### File structure

```
heart-rate-monitor.html   the entire app (HTML/CSS/JS in one file)
manifest.json             web app manifest (name, icons, theme color)
sw.js                     service worker for offline caching
icons/
  favicon-16.png
  favicon-32.png
  apple-touch-icon.png
  icon-192.png
  icon-512.png
  icon-512-maskable.png
README.md                 this file
```

---

## Using the app

| Action | What it does |
|---|---|
| **Connect** | Opens the browser's Bluetooth device picker to pair with a new sensor. |
| **Quick connect to last sensor** | Reconnects to the previously paired sensor without showing the picker. |
| **Age** (Settings) | Used to compute your max HR (`220 − age`) and training zones. |
| **Moving average window** (Settings) | How many seconds of readings are averaged into the smoothed chart line, recalculated every second. |
| **Start Workout / Finish Workout** | Begins/ends a tracked session with zone-time accounting. |
| **Measure 2-min recovery after finish** | If on, Finish Workout starts a 2-minute recovery countdown before showing the summary; if off, the summary appears immediately. |
| **Export data (CSV)** | Downloads the current workout (or full session, if no workout was run) as a CSV file. |
| **Keep screen awake** | Prevents the screen from timing out while the app is open in the foreground. |
| **Battery saver display** | Switches to a minimal black screen with just the BPM number; tap it to return to the full view. |
| **Reset session** | Clears all readings, stats, and the saved/restorable session. |

---

## Known limitations

- **Chrome on Android only.** Web Bluetooth isn't supported in Safari/iOS, and desktop browser support varies.
- **No true lock-screen operation.** Like any web page, this app pauses when the screen locks — "Keep screen awake" and "Battery saver display" reduce the impact but can't fully replace a native app's background BLE access.
- **No real MAC address access.** Browsers deliberately don't expose a sensor's actual Bluetooth address to web pages; "Quick Connect" instead remembers your specific paired sensor via the browser's own permission system.
- **HRV and stress figures are estimates**, useful for personal tracking trends — not a clinical or medical measurement.

---

## Standards used

- [Bluetooth Heart Rate Service (0x180D)](https://www.bluetooth.com/specifications/specs/heart-rate-service-1-0/)
- [Heart Rate Measurement characteristic (0x2A37)](https://www.bluetooth.com/specifications/specs/gatt-specification-supplement/)
- [Web Bluetooth API](https://webbluetoothcg.github.io/web-bluetooth/)
