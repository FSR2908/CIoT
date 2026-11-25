# CIoT — Civilians IoT Studio
### *A High-Performance Browser-Based IoT & Microcontroller Simulator (Wokwi Clone)*

![CIoT](https://img.shields.io/badge/CIoT-Simulator-007acc?style=for-the-badge&logo=arduino)
![Wokwi Compatible](https://img.shields.io/badge/Wokwi-Compatible_diagram.json-00ffcc?style=for-the-badge)
![Zero Install](https://img.shields.io/badge/Runs_100%25-In_Browser-28a745?style=for-the-badge)

**CIoT (Civilians IoT Studio)** is an open-source, web-native electronics workbench and microcontroller simulator modeled after **Wokwi**. It is built using the visual component specifications and pin architectures from [`wokwi-elements`](https://github.com/wokwi/wokwi-elements) and the simulation/diagram guidelines from [`wokwi-docs`](https://github.com/wokwi/wokwi-docs).

---

## 🚀 Key Features

### 1. Interactive Circuit Canvas & Breadboard
* **High-Fidelity Virtual Hardware**:
  * **Microcontrollers**: Arduino Uno R3, Arduino Nano, ESP32 DevKit V1.
  * **Displays**: LCD 1602 (I2C / 4-bit parallel), 16-LED NeoPixel RGB Ring.
  * **Outputs & Actuators**: 5mm LEDs (Red, Green, Blue, Yellow, Orange, White, Purple) with realistic luminescence and PWM brightness fading, SG90 Micro Servo Motor with animated rotating horn (0–180°), Piezo Buzzer with real Web Audio tone synthesis.
  * **Sensors**: HC-SR04 Ultrasonic Distance Sensor (with live distance range slider), DHT22 Climate Sensor (with Temperature & Humidity sliders).
  * **Inputs**: Momentary Pushbuttons with tactile press animation, Rotary Potentiometer with mouse drag rotation & ADC telemetry.
  * **Passives**: Resistors with dynamically computed 4-band EIA color codes (220Ω, 1kΩ, 10kΩ, etc.).
* **Orthogonal Wire Routing Engine**:
  * Click any component pin to start a wire; click another pin to connect.
  * Wire color selector (Green, Red, Blue, Yellow, Orange, Purple, Cyan, White, Black).
  * 90° Manhattan stepped routing with visual bend points.
  * Select wires to inspect or press `Delete` / `Backspace` to remove.
* **Full Canvas Navigation**:
  * Infinite panning (Space + mouse drag or middle-click drag).
  * Smooth zoom (mouse wheel or floating zoom toolbar: +, -, 100%, Fit View).
  * Rotation (`R` key or toolbar button rotates selected part by 90°).

### 2. Real-Time C++/Arduino Simulation Engine
* **Browser-Native Virtual Machine**:
  * Runs sketches directly in the browser with **zero local installation** and **no cloud compiler queues**.
  * Non-blocking scheduler driven by `requestAnimationFrame` and microtask stepping.
  * Accurately emulates:
    * `pinMode(pin, INPUT / OUTPUT / INPUT_PULLUP)`
    * `digitalWrite(pin, HIGH / LOW)`
    * `digitalRead(pin)`
    * `analogWrite(pin, pwm)` (0–255 PWM fading)
    * `analogRead(pin)` (0–1023 10-bit ADC for potentiometers & sensors)
    * `delay(ms)`, `delayMicroseconds(us)`, `millis()`, `micros()`
    * `pulseIn(pin, val)` (calculates sonar round-trip time for HC-SR04)
    * `tone(pin, freq)` & `noTone(pin)` (synthesizes audio via Web Audio API)
    * `LiquidCrystal_I2C` (renders text to virtual 16x2 dot-matrix LCD)
    * `Servo` library (animates SG90 servo horn)

### 3. Full-Featured Serial Monitor
* Real-time streaming terminal for `Serial.print` and `Serial.println`.
* Interactive command input field with carriage return / newline options (`LF`, `CR`, `CRLF`, `None`).
* Baud rate selector (9600, 19200, 38400, 57600, 115200).
* Timestamps toggle and Autoscroll.
* Clear console and collapsible dock.

### 4. Code Editor & diagram.json Compatibility
* Multi-file tabs:
  * `sketch.ino`: C++ / Arduino sketch with syntax highlighting, line numbers, and auto-indentation.
  * `diagram.json`: Live two-way synchronized circuit specification compliant with Wokwi's format. Edits in the canvas update the JSON, and manual edits in JSON instantly update the canvas.
  * `libraries.txt`: Simulated libraries manifest.
* Keyboard shortcuts:
  * <kbd>Ctrl</kbd> + <kbd>Enter</kbd>: Run / Pause Simulation
  * <kbd>Ctrl</kbd> + <kbd>S</kbd>: Save Project to browser local storage
  * <kbd>R</kbd>: Rotate selected part 90°
  * <kbd>Delete</kbd> / <kbd>Backspace</kbd>: Delete selected part or wire

### 5. Preloaded Project Presets
1. 🌟 **Blink (Hello World)**: Arduino Uno + Pin 13 LED + 220Ω Resistor.
2. 🚦 **Traffic Light System**: 3 LEDs (Red, Yellow, Green) cycling through intersection states.
3. 🔘 **Pushbutton LED Controller**: Button with `INPUT_PULLUP` controlling an external blue LED.
4. 🎛️ **Potentiometer PWM Dimmer**: Rotary potentiometer on A0 modulating LED brightness with live telemetry.
5. 📟 **LCD 1602 Hello World**: 16x2 LCD display showing live uptime counter and custom messages.
6. 🦾 **Servo Motor Sweep**: SG90 Servo oscillating between 0° and 180°.
7. 📡 **Ultrasonic Distance Radar**: HC-SR04 sonar sensor with warning buzzer and proximity alarm.

---

## 🛠️ Getting Started

### Prerequisites
* [Node.js](https://nodejs.org/) (v18+) and `npm`

### Installation
```bash
# In the repository root:
npm install
```

### Running Locally (Development Mode)
```bash
npm run dev
```
Open your browser at `http://localhost:3000` (or the port displayed in the console).

### Building for Production
```bash
npm run build
```
Generates a standalone, fully self-contained bundle in the `dist/` directory.

### Previewing the Production Build
```bash
npm run preview
```

---

## 📁 Project Architecture

```
Civilians IoT/
├── index.html                 # App HTML entry point
├── package.json               # Package configuration & scripts
├── tsconfig.json              # TypeScript configuration
├── vite.config.ts             # Vite build configuration
├── src/
│   ├── main.ts                # Application bootstrap & UI state management
│   ├── styles.css             # Dark theme styles & UI layout
│   ├── types/
│   │   └── index.ts           # Diagram, Part, Pin, and Simulation types
│   ├── parts/
│   │   ├── catalog.ts         # Component definitions, pin maps, and SVG graphics
│   │   └── palette.ts         # Searchable parts drawer & catalog UI
│   ├── canvas/
│   │   └── circuit-canvas.ts  # Circuit canvas, interactive wiring, drag/pan/zoom
│   ├── simulator/
│   │   └── engine.ts          # Arduino C++ interpreter & virtual netlist runtime
│   ├── editor/
│   │   └── code-editor.ts     # Multi-tab code editor with syntax highlighting
│   ├── serial/
│   │   └── serial-monitor.ts  # Real-time Serial Monitor with input & baud control
│   ├── docs/
│   │   └── docs-modal.ts      # Built-in reference modal for pinouts & diagram.json
│   └── examples/
│       └── presets.ts         # Preloaded project templates
├── wokwi-docs/                # Cloned reference: Wokwi documentation
└── wokwi-elements/            # Cloned reference: Wokwi web components
```

---

## 📄 License
MIT License. Inspired by the open-source projects of Uri Shaked and the Wokwi community.
