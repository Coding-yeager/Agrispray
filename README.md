# 🌾 **Agrispray – Vision-Based Precision Spraying Hexacopter**

![License: CC BY-SA 4.0](https://img.shields.io/badge/License-CC_BY--SA_4.0-brightgreen.svg)
![Python 3.8+](https://img.shields.io/badge/Python-3.8%2B-blue)
![YOLO](https://img.shields.io/badge/YOLO-v11-orange)

**Author: Nikhil**

Agrispray is an autonomous agricultural spraying hexacopter that combines **YOLOv11 plant detection**, **ArUco-based localization**, and **ESP32 spray control** for precision pesticide delivery. This system reduces chemical usage while maximizing accuracy in modern precision agriculture.

---

## 🚀 **Key Features**

- ✈️ **Hexacopter Flight Platform** – Multi-rotor stability and maneuverability
- 🤖 **YOLOv11 Plant Detection** – Real-time crop identification with confidence scoring
- 🧭 **ArUco Marker Localization** – Accurate indoor/outdoor pose estimation
- 💧 **Intelligent Spray Control** – Confidence-based variable spray duration
- 📡 **ESP32 Integration** – Wireless PWM-based motor/pump control
- 📊 **Telemetry Logging** – Complete flight data and spray records
- 🛡️ **Open Source** – CC BY-SA 4.0 licensed

---

## 📂 **Repository Structure**

```
Agrispray/
│
├── README.md                          # Project documentation
├── cad_model/                         # 3D CAD designs & mechanical components
│   └── LICENSE
│
├── Classifier_model/                  # YOLO Detection System
│   ├── app.py                         # Detection application
│   ├── yolov11.ipynb                  # Training & evaluation notebook
│   └── YOLO_results/
│       ├── best.pt                    # Best trained model
│       └── last.pt                    # Last checkpoint
│
├── electrical/                        # Electrical schematics & wiring diagrams
│
├── esp_code/                          # ESP32 Firmware
│   ├── pwm_flow.csv                   # PWM timing reference
│   └── motor_template/
│       ├── generate_pwm_code.py       # PWM code generator
│       ├── motor_template.ino         # ESP32 firmware template
│       └── pwm_flow.csv               # Flow control specifications
│
└── localization/                      # ArUco Localization & Telemetry
    ├── app.py                         # Main localization application
    ├── best.pt                        # Model checkpoint
    ├── last.pt                        # Last model state
    └── aruco/
        ├── captureImagesandPose.py    # Image & pose capture utility
        ├── export_kml_gpx.py          # GPS data export tool
        ├── extract_flight_metadata.py # Flight data extractor
        ├── k.py                       # Camera calibration module
        ├── triliteration_solver.py    # 3D position solver
        ├── transfor.py                # Coordinate transformation
        └── requirements.txt           # Python dependencies
```

---

## ⚙️ **System Architecture**

### **Detection Pipeline**
```
Live Camera Feed → YOLOv11 Inference → Crop Confidence → Spray Duration
```

### **Localization Pipeline**
```
ArUco Markers → Pose Estimation → Trilateration → GPS Coordinates
```

### **Spray Control**
```
Confidence Score → ESP32 Command → PWM Signal → Motor/Pump Control
```

Spray duration is calculated as:
$$T_{spray} = C_{avg} \times 62 \text{ ms}$$

where $C_{avg}$ is the average confidence score from YOLO detection.

---

## 🛠️ **Installation & Setup**

### **Prerequisites**
- Python 3.8 or higher
- pip (Python package manager)
- USB connection to ESP32 (for spray control)
- Camera with appropriate mount
- ArUco markers (printed calibration pattern)

### **Step 1: Clone the Repository**
```bash
git clone https://github.com/Coding-yeager/Agrispray.git
cd Agrispray
```

### **Step 2: Install Dependencies**
```bash
pip install -r localization/aruco/requirements.txt
```

### **Step 3: Camera Calibration (First Time Only)**
```bash
cd localization/aruco
python k.py
```

### **Step 4: Configure ESP32**
1. Open `esp_code/motor_template/motor_template.ino` in Arduino IDE
2. Select your ESP32 board and COM port
3. Upload firmware to ESP32

---

## 🧠 **How It Works**

### **🔍 YOLOv11 Detection** ([Classifier_model](Classifier_model))
- Detects crop/plant regions in real-time
- Outputs bounding boxes and confidence scores
- Filters by confidence threshold (configurable)
- Runs on single GPU or CPU (slower)

### **🧭 ArUco Localization** ([localization/aruco](localization/aruco))
- Detects ArUco markers in camera frame
- Computes drone pose (position + orientation)
- Uses trilateration for 3D positioning
- Exports GPS/KML coordinates

### **💧 Spray Control** ([esp_code](esp_code))
- Maps YOLO confidence → spray duration
- Sends JSON commands via UART to ESP32
- Controls PWM signal to spray pump
- Logs all spray events with timestamps

### **📡 Telemetry & Data Logging**
- Timestamped detection records
- Spray duration & location logs
- Flight path visualization
- KML/GPX export for mapping tools

---

## ▶️ **Running the System**

### **Option 1: Detection Only**
```bash
cd Classifier_model
python app.py
```

### **Option 2: Localization Only**
```bash
cd localization
python app.py
```

### **Option 3: Full System (Detection + Localization + Spray)**
```bash
# Terminal 1: Start Detection
cd Classifier_model
python app.py

# Terminal 2: Start Localization & Spray Control
cd localization
python app.py
```

### **Export Flight Data**
```bash
cd localization/aruco
python export_kml_gpx.py
```

---

## 📊 **Configuration**

### **YOLO Detection Parameters** (`Classifier_model/app.py`)
- `confidence_threshold`: Minimum detection confidence (0.5–0.95)
- `iou_threshold`: Non-max suppression threshold (0.4–0.7)
- `source`: Camera index or video file path

### **Spray Control Parameters** (`esp_code/motor_template/motor_template.ino`)
- `BASE_DURATION`: Minimum spray time (ms)
- `CONFIDENCE_MULTIPLIER`: 62 ms per unit confidence
- `COM_PORT`: ESP32 serial connection

### **Localization Parameters** (`localization/aruco/k.py`)
- `MARKER_SIZE`: ArUco marker dimension (meters)
- `CAMERA_MATRIX`: Calibrated intrinsics (auto-computed)
- `DIST_COEFFS`: Distortion coefficients (auto-computed)

---

## 📈 **Model Training**

To retrain the YOLO detector with custom dataset:

```bash
cd Classifier_model
jupyter notebook yolov11.ipynb
```

The notebook includes:
- Dataset loading from RoboFlow (yellow_leafs-pr0v9)
- YOLOv11 model training
- Evaluation metrics & visualizations
- Model export & deployment

---

## 🎯 **Typical Workflow**

1. **Preparation**
   - Mount camera & ArUco markers on hexacopter
   - Calibrate camera (`localization/aruco/k.py`)
   - Program ESP32 with spray firmware

2. **Pre-Flight**
   - Test camera feed and detection accuracy
   - Verify ArUco marker detection
   - Check spray pump connectivity

3. **Operation**
   - Launch detection and localization scripts
   - Take off and navigate over target crops
   - System automatically sprays based on plant detection

4. **Post-Flight**
   - Review telemetry logs
   - Export KML/GPX coordinates
   - Analyze spray coverage and efficiency

---

## 📁 **Key Files Overview**

| File | Purpose |
|------|---------|
| [Classifier_model/app.py](Classifier_model/app.py) | YOLO detection interface |
| [localization/app.py](localization/app.py) | ArUco localization engine |
| [esp_code/motor_template/motor_template.ino](esp_code/motor_template/motor_template.ino) | ESP32 spray control firmware |
| [localization/aruco/k.py](localization/aruco/k.py) | Camera calibration |
| [localization/aruco/triliteration_solver.py](localization/aruco/triliteration_solver.py) | 3D position calculation |
| [localization/aruco/export_kml_gpx.py](localization/aruco/export_kml_gpx.py) | GPS data export |

---

## 🐛 **Troubleshooting**

### Camera not detected
```bash
# List available cameras
python -c "import cv2; print(cv2.getBuildInformation())"
```

### ArUco markers not detected
- Ensure adequate lighting
- Check marker size matches configuration
- Verify camera calibration file exists

### ESP32 connection issues
- Confirm correct COM port in script
- Install CH340 drivers if needed
- Check USB cable quality

### Low detection confidence
- Retrain model with more dataset samples
- Adjust confidence threshold
- Check lighting and camera focus

---

## 📚 **Dependencies**

Core dependencies (see [localization/aruco/requirements.txt](localization/aruco/requirements.txt)):
- **opencv-python** – Computer vision
- **ultralytics** – YOLO detection
- **opencv-contrib-python** – ArUco markers
- **scipy** – Trilateration calculations
- **numpy** – Numerical computing
- **pyyaml** – Configuration files

---

## 🔗 **Related Resources**

- [YOLOv11 Documentation](https://docs.ultralytics.com/)
- [OpenCV ArUco Module](https://docs.opencv.org/master/d5/dae/tutorial_aruco_detection.html)
- [ESP32 Arduino Core](https://github.com/espressif/arduino-esp32)
- [RoboFlow Datasets](https://roboflow.com/)

---

## 📝 **License**

This project is released under the **Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)** license.

**You are free to:**
- Share, copy, and redistribute the material
- Adapt, remix, and build upon the material

**Under these conditions:**
- **Attribution** – Give appropriate credit to the original authors
- **ShareAlike** – Any derivative works must use the same license

See the full license in [cad_model/LICENSE](cad_model/LICENSE).

---

## 👥 **Contributors**

**Author: Nikhil**

- [Nikhil](https://github.com/Nikhil) – YOLO Model & Detection
- [Nikhil](https://github.com/Nikhil) – Localization & ESP32 Integration

---

## 📧 **Support & Contact**

Found a bug? Have a question? Need clarification?

- **Open an Issue** on GitHub
- **Reach out** to the project contributors
- **Check existing discussions** before posting

---

## 🎓 **Citation**

If you use Agrispray in your research, please cite:

```bibtex
@project{agrispray2024,
  title={Agrispray: Vision-Based Precision Spraying Hexacopter},
  author={Nikhil},
  year={2024},
  url={https://github.com/Coding-yeager/Agrispray}
}
```

---

**Made with ❤️ by Nikhil**