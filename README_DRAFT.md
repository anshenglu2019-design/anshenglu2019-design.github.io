<div align="center">

# An-Sheng (Anson) Lu
### Electrical Engineering Undergraduate | UC Riverside
**Research Assistant (RaMS Lab) | IEEE Projects Chair | FSAE Electrical Intern**

*[Email](mailto:anshenglu2019@gmail.com) | [LinkedIn](https://linkedin.com/in/an-sheng-lu-2289462b5) | [Download Resume (PDF)](assets/An-Sheng_Lu_Resume.pdf)*

</div>

---

## 🛠 Engineering Projects & Research

### 1. Occlusion-Free Growth Monitoring (RaMS Lab)
*   **Problem:** Traditional length-based larval measurements fail in high-density environments due to occlusion and segmentation noise.
*   **Approach:** Engineered an end-to-end pipeline using **AISFormer** for instance segmentation and topological skeleton extraction.
*   **Insight:** Shifting to **median-width statistical modeling** across the skeleton’s central 80% provides local invariance to partial occlusion and eliminates head/tail artifacts.
<p align="center">
  <img src="assets/inference_result.jpg" width="500" alt="ML Inference Result">
  <br><em>Figure 1: Real-time inference showing instance segmentation in dense scenes.</em>
</p>

### 2. Embedded Systems & Hardware (Formula SAE)
*   **Problem:** Bridging theoretical circuit design with physical constraints in a high-performance racing environment.
*   **Approach:** Designed and routed control circuits for sensor integration using **Altium Designer** with a focus on trace routing and footprint mapping.
*   **Insight:** Effective hardware design requires balancing logical requirements with physical manufacturability, noise rejection, and board geometry.
<p align="center">
  <img src="assets/pcb_layout.png" width="500" alt="PCB Layout">
  <br><em>Figure 2: Control circuit layout and trace routing in Altium Designer.</em>
</p>

### 3. Robotics & Control Systems (DLR Internship)
*   **Problem:** Achieving stable, predictable trajectory tracking in physical robotic systems.
*   **Approach:** Integrated OpenManipulator-X arm; modeled mechanical resonance and power dissipation in **MATLAB/Simulink**.
*   **Insight:** Platform stability is governed by damping behavior; applying critically damped feedback systems is essential for minimizing oscillations.
<p align="center">
  <a href="https://youtu.be/rqDFj4NV7VE"><img src="assets/robotic_arm_thumbnail.png" width="500"></a>
  <br><em>Video 1: Hardware-in-the-loop motion control demo.</em>
</p>

---

## 🏆 Patent — System-Level Engineering
**Certified Invention Patent Holder (Patent No. 1894025)**
*   **Design:** ESP32 + MPU6050 integration for motion telemetry.
*   **Logic:** Engineered deterministic 10ms-interval sampling loops for real-time velocity estimation and high-precision zero-crossing detection.
*   **Robustness:** Implemented state-based reset with hysteresis to reject noise during non-linear movement.
<p align="center">
  <img src="assets/baton_prototype.png" width="300">
  <br><em>Figure 3: Physical PCB prototype and directional signaling assembly.</em>
</p>

---

## 🎓 Education & Leadership

| Category | Details |
| :--- | :--- |
| **Education** | B.S. Electrical Engineering, UCR (Expected 2029) |
| **GPA** | 3.78 / 4.00 |
| **Leadership** | Incoming Projects Chair, IEEE UCR Branch |
| **Competitions** | All Ring Finalist, 1st Place (Robotics/Industrial Robot) |

### 🛠 Technical Proficiency
* **Languages:** C/C++, Python (PyTorch, OpenCV), MATLAB/Simulink, LabVIEW
* **Engineering Tools:** Altium Designer, SolidWorks, ROS (Basic), Arduino/ESP32, Git
