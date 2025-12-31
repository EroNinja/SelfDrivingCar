# 🚗 **Self-Driving Car Using Computer Vision and Embedded Control**

### *Final Year AI & Embedded Systems Project*

This project implements an **autonomous lane-following vehicle** using **classical computer vision on a Raspberry Pi** and **low-level motor control via an Arduino Uno**.  
The system runs **fully on-device**, under constrained hardware resources, while maintaining **stable lane detection**, **smooth steering corrections**, and **reliable lane termination handling**.

---

## **1. Problem Statement**

Autonomous lane following in real-world environments presents multiple challenges:

- **Perspective distortion** due to camera placement  
- **Variable lighting** and visual noise  
- **Real-time constraints** on limited compute hardware  
- **Tight coupling** between perception and actuation  

The objective of this project was to design and implement a **complete perception-to-control pipeline** capable of:

- Accurately detecting lane boundaries  
- Keeping the vehicle centered within the lane  
- Executing **graded steering corrections**  
- Detecting lane termination and safely stopping or turning  

All computation is performed **locally**, without cloud services or pretrained deep learning models.

---

## **2. System Overview**

The system follows a **master–slave architecture**, separating perception from actuation.

### **Raspberry Pi (Master)**

- Captures live video from the camera  
- Performs all image processing and lane detection  
- Computes steering decisions  
- Sends encoded movement commands via **GPIO**  

### **Arduino Uno (Slave)**

- Reads GPIO command signals  
- Controls motor direction and speed  
- Executes predefined movement primitives

### **Camera → Raspberry Pi (OpenCV) → GPIO Signals → Arduino → Motor Driver → Wheels**


This separation ensures **deterministic motor control** while allowing complex vision logic to run independently.

---

## **3. Hardware Architecture**

### **Components**

- Raspberry Pi  
- Raspberry Pi Camera Module  
- Arduino Uno  
- L298 Dual Channel H-Bridge Motor Driver  
- DC motors with car chassis  
- Portable power supply  
- Jumper wires and USB cables  

### **Camera Placement**

The camera is mounted at a **downward-facing acute angle**, maximizing visibility of lane markings while minimizing irrelevant background information.

---

## **4. Computer Vision Pipeline**

All image processing is implemented using **OpenCV** and optimized for **real-time execution**.

### **4.1 Frame Acquisition**

- Continuous RGB frames captured at **400 × 240 resolution**  
- Frames converted from **BGR → RGB**

---

### **4.2 Region of Interest (ROI)**

Only a **trapezoidal region** corresponding to the road surface is processed.

**Benefits:**
- Reduces background noise  
- Lowers computational overhead  
- Improves detection stability  

---

### **4.3 Perspective Transformation (Bird’s-Eye View)**

The trapezoidal ROI is transformed into a **rectangular bird’s-eye view** using a homography matrix.

**Advantages:**
- Lane lines become approximately vertical  
- Simplifies lane geometry  
- Enables reliable histogram-based detection  

---

### **4.4 Preprocessing**

After perspective correction:

- **Grayscale conversion** removes color dependency  
- **Thresholding** isolates bright lane markings  
- **Canny edge detection** enhances lane boundaries  
- Threshold and edge maps are **combined** for robustness  

---

### **4.5 Lane Detection Using Histograms**

Lane positions are detected using **column-wise histograms**:

- Pixel intensities are summed vertically  
- Peaks in the histogram correspond to lane boundaries  
- Left and right lanes are detected independently  

This method is **fast**, **deterministic**, and effective for structured lane environments.

---

### **4.6 Lane Center Calculation**

The lane center is computed as:
lane_center = (right_lane − left_lane) / 2 + left_lane
offset = lane_center − frame_center


The resulting **offset** determines steering direction and magnitude.

---

### **4.7 Lane End Detection**

A full-frame histogram is used to detect the **absence of lane markings**.

If total lane intensity drops below a threshold:

- The lane is considered **terminated**  
- A **stop or U-turn** maneuver is triggered  

---

## **5. Decision Logic**

Steering decisions are **discretized**, not continuous.

### **Supported Actions**

- **Forward**  
- **Left turns** (3 levels: mild → sharp)  
- **Right turns** (3 levels: mild → sharp)  
- **U-turn**  
- **Stop**  

Discrete control improves stability and reduces oscillations under noisy perception.

---

## **6. Raspberry Pi → Arduino Communication**

Movement commands are transmitted using **4 GPIO pins**, encoded as a **4-bit binary value**.

| **Binary Code** | **Decimal** | **Action** |
|----------------|------------|-----------|
| 0000 | 0 | Forward |
| 0001–0011 | 1–3 | Right Turns |
| 0100–0110 | 4–6 | Left Turns |
| 0111 | 7 | U-Turn |
| ≥1000 | ≥8 | Stop |

This avoids serial communication latency and ensures **fast, reliable command transfer**.

---

## **7. Motor Control (Arduino)**

The Arduino:

- Reads GPIO inputs  
- Decodes movement commands  
- Adjusts motor speed using **PWM**  
- Controls direction via **H-bridge logic**  

Turning is achieved using **differential motor speeds**, while U-turns are executed using predefined motion sequences for reliability.

---

## **8. Software Stack**

### **Languages**

- **C++** (Raspberry Pi)  
- **Arduino C**

### **Libraries & Tools**

- OpenCV  
- WiringPi  
- Arduino IDE  
- Raspberry Pi OS  

---

## **9. Installation & Setup (Optional)**

This project requires **OpenCV built from source** on Raspberry Pi.  
Detailed installation steps are included for reproducibility but are **not required** to understand the system.

<details>
<summary><b>OpenCV Installation Steps</b></summary>

(Add your OpenCV build steps here if needed.)

</details>

---


---

## **10. Performance Characteristics**

- **Real-time execution** on Raspberry Pi  
- Stable lane tracking under controlled lighting  
- Smooth, graded steering corrections  
- Reliable lane termination detection  

---

## **11. Limitations**

- Sensitive to extreme lighting changes  
- Assumes visible lane markings  
- Fixed camera calibration  
- Discrete steering instead of continuous PID control  

---

## **12. Future Improvements**

- PID-based steering control  
- Adaptive thresholding  
- ML-based lane segmentation  
- Obstacle detection  
- ROS-based modular architecture  
- Dynamic camera calibration  

---

## **13. Notes**

This repository contains **academic project code**, cleaned and documented for clarity and educational purposes.  
The **system design, algorithms, and implementation** reflect the original work performed during the project.

*This README was refined and formatted with the assistance of AI tools to improve clarity and structure.*








