<div align="center">

# An-Sheng (Anson) Lu
### Electrical Engineering Undergraduate | UC Riverside
**Research Assistant (Computer Vision & Smart Agriculture)**
*Designing occlusion-robust vision systems, embedded architectures, and cyber-physical pipelines.*

[📧 anshenglu2019@gmail.com](mailto:anshenglu2019@gmail.com) | [🔗 LinkedIn](https://linkedin.com/in/an-sheng-lu-2289462b5) | [📄 Download Resume](assets/An-Sheng_Lu_Resume.pdf)

</div>

---

## 🔬 Featured Project — Research Depth
### Occlusion-Free Mealworm Growth Monitoring System
*Robotics and Medical Systems (RaMS) Lab, UCR*

**🧩 Problem**
In high-density larval farming environments, individuals frequently overlap and occlude each other, making traditional measurement unreliable. Naive approaches fail due to segmentation noise and posture variation.
<br>

**⚙️ System Architecture**
*   **Image Acquisition:** iPhone 15 Pro (controlled lighting)
*   **Pipeline:** Pre-filtering (HSV/BGR) → AISFormer segmentation → Topological skeleton extraction → Width estimation
<br>

**🧠 Core Insight**
Instead of estimating length, the system measures **median width across the central 80% of the larval skeleton.** 
*   Width is locally invariant under partial occlusion.
*   Central region avoids head/tail segmentation noise.
<br>

**🔧 Engineering Design Decisions**
1.  **Robustness:** Replaced mean with median thickness to reduce sensitivity to outliers.
2.  **Noise Handling:** Applied z-score filtering (> 2.5σ) to eliminate disconnected fragments.
3.  **Data Strategy:** Engineered a synthetic pipeline compositing masks onto real backgrounds.

---

## 🧠 Engineering Projects

### 🛠 Embedded Systems & Hardware (Formula SAE)
*Electrical Subsystems Intern — Highlander Racing, UCR*
* **Problem:** Bridging theoretical circuit design with real-world manufacturable PCB layouts.
* **Approach:** Designed control circuit with sensor interface; implemented dual-layer PCB in **Altium Designer**.
* **Key Learning:** Hardware design is constrained by physical routing, noise, and manufacturability.

### 🤖 Robotics & Control Systems (DLR Internship)
*German Aerospace Center (DLR)*
* **Problem:** Achieving stable motion control in real robotic systems.
* **Approach:** Integrated OpenManipulator-X arm; modeled dynamics in **MATLAB/Simulink**.
* **Key Insight:** System stability is governed by damping behavior, not just control accuracy.

### 💡 Embedded Firmware Systems
* **Problem:** Designing responsive firmware with real-time inputs.
* **Approach:** Built 3×3×3 LED matrix firmware (Arduino Micro) with ADC-based control.
* **Key Insight:** Systems require tight coupling between hardware timing and software logic.

---

## 🏆 Patent — System-Level Engineering
**Certified Invention Patent Holder (Patent No. 1894025)** | *Taiwan Intellectual Property Office*

*   **System Design:** ESP32 + MPU6050 IMU integration with real-time motion pipeline.
*   **Mechanism:** Deterministic 10ms sampling, numerical integration for velocity estimation, and zero-crossing detection.
*   **Reliability:** State-machine reset with hysteresis; robust noise rejection.
*   **Takeaway:** Reliable embedded systems require temporal stability + signal conditioning.

---

## 🎓 Quick Reference

| Category | Highlights |
| :--- | :--- |
| **Competitions** | All Ring Finalist, 1st Place (Robotics & Industrial Robot) |
| **Education** | B.S. Electrical Engineering, UCR (2029) |
| **Leadership** | Incoming Projects Chair, IEEE Student Branch |
| **Tech Focus** | Computer Vision (PyTorch), Embedded (ESP32), Altium, C++/Python |

---
<div align="center">
  <sub>An-Sheng Lu © 2026</sub>
</div>
