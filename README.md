

# 🌾 **Agrispray – Vision-Based Precision Spraying Hexacopter**

**Author: Nikhil**

Agrispray is a vision-based agricultural spraying drone built for autonomous precision pesticide delivery using YOLO-based plant detection and ArUco-based localization.
This repository includes the **CAD designs, flight code, localization scripts, detection models, and documentation**.

---

# 🚀 **Project Overview**

The AgriSpray Hexacopter integrates:

* **Hexacopter flight platform**
* **Vision-based plant detection (YOLOv8)**
* **ArUco marker–based indoor/outdoor localization**
* **ESP32-based spray control system**
* **Automated confidence-to-spray mapping**
* **Telemetry logging and communication**

This system aims to reduce chemical usage and increase accuracy in precision agriculture.

---

# 📂 **Repository Structure**

```
Agrispray/
│
├── CAD/                          # 3D printable components & mechanical design
├── Firmware/                     # Flight controller & ESP32 code
├── Yolo_results/                 # Detection scripts, outputs & models
│   ├── Yolo_results/             
│   │   └── Aruco/
│   │       └── requirements.txt  # Localization script dependencies
│
├── Scripts/                      # Utility python scripts
├── Data/                         # Collected test datasets
├── Docs/                         # Documentation & diagrams
└── README.md                     # Project documentation
```

---

# ⚙️ **Environment Setup**

### 📌 Install Dependencies for Localization & Detection

The `requirements.txt` file is located at:

```
Yolo_results/Yolo_results/Aruco/requirements.txt
```

Install all required packages using:

```bash
pip install -r Yolo_results/Yolo_results/Aruco/requirements.txt
```

---

# 🧠 **How It Works**

### 🔍 Plant Detection

YOLOv8 identifies crop regions and outputs confidence values.

### 🧭 Localization

ArUco markers allow the drone to compute pose and track movement.

### 💧 Spray Control

The YOLO confidence value determines spray duration via:

```
T_spray = C_avg × 62 ms
```

Sent to the ESP32 in JSON format via UART.

### 📡 Telemetry

All detection events and spray logs are stored for analysis.

---

# ▶️ **Running the System**

### 1. Start YOLO detection

```bash
python detect.py
```

### 2. Start ArUco localization

```bash
python localization.py
```

### 3. Connect ESP32 (USB / UART)

Make sure correct COM port is selected in the script config.

---

# 📝 **License**

This project is released under:

## **Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)**

You are free to:

* **Share** — copy and redistribute
* **Adapt** — remix, transform, build upon the material

Under the conditions:

* **Attribution** — give credit to the original authors
* **ShareAlike** — released adaptations must use the same license

See the full license text in the `LICENSE` file.

---

# 👥 **Contributors**

**Author: Nikhil**

* Nikhil
* Nikhil

---

# 📧 Contact

If you have questions, feel free to raise an issue or contact the project contributors.

---


