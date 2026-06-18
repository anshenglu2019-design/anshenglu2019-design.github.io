<div align="center">

# An-Sheng (Anson) Lu
### Electrical Engineering Undergraduate | UC Riverside
**Research Assistant (RaMS Lab) | IEEE Projects Chair (Incoming) | FSAE Electrical Subsystems Intern**

*[Email](mailto:anshenglu2019@gmail.com) | [LinkedIn](https://linkedin.com/in/an-sheng-lu-2289462b5) | [Download Resume (PDF)](assets/An-Sheng_Lu_Resume.pdf)*

</div>

---

##  Engineering Projects & Research

### 1. Research Assistant | Mealworm Growth Monitoring (RaMS Lab)
*   **Problem:** Traditional growth monitoring involves manual labor and significant time-costs.
*   **Approach:** Engineered an end-to-end **Computer Vision and Deep Learning** pipeline using an **Amodal Instance Segmentation Transformer (AISFormer)** for mealworm detection and growth width measurement for classification in high-occlusion, overlapping environments.
*   **Insight:** Shifting to **median-width statistical modeling** across the skeleton’s central 90% provides local invariance to partial occlusion and eliminates head/tail noise.

<p align="center">
  <img src="assets/inference_result.jpg" width="500" alt="ML Inference Result">
  <br><em>Figure 1: Inference showing worm detection and width measurement in dense scenes.</em>
</p>

<br>

### 2. FSAE Electrical Intern | Embedded Systems & Hardware (Highlander Racing)
*   **Problem:** Bridging theoretical circuit design with physical manufacturing constraints.
*   **Approach:** Designed and routed control circuits for sensor integration using **Altium Designer** with a focus on trace routing and component footprint mapping.
*   **Insight:** Effective hardware design requires balancing logical circuit requirements with physical manufacturability, noise rejection, and board geometry.

<p align="center">
  <img src="assets/pcb_layout.png" width="500" alt="PCB Layout">
  <br><em>Figure 2: Initial automated control circuit layout and trace routing in Altium Designer.</em>
</p>

<br>

### 3. Robotics Intern | Robotics and Control Systems (German Aerospace Center - DLR)
*   **Problem:** Achieving stable, predictable trajectory tracking in physical robotic systems.
*   **Approach:** Integrated an **OpenManipulator-X** arm; customized a track system with **3D-printed structural interfaces** to extend the platform's workspace.
*   **Insight:** Achieving stable motion required implementing custom control loops to manipulate physical parts; programmed the system to execute complex trajectory tracking.

<p align="center">
  <a href="https://youtu.be/rqDFj4NV7VE"><img src="assets/robotic_arm_thumbnail.png" width="500"></a>
  <br><em>Video 1: Hardware-in-the-loop motion control demo.(Click to watch on YouTube)</em>
</p>

#### Hardware Prototyping & Controls Validation:
*   **Dynamic System Modeling:** Performed structural physics modeling mass-spring-damper system in **MATLAB/Simulink**, achieving a highly stable, **critically damped system response**.
*   **Embedded Firmware:** Developed a 3x3x3 LED matrix using **Arduino Micro**, featuring a 4-mode firmware architecture with **real-time ADC-based pace control**.
    
<table align="center">
  <tr>
    <td width="50%" align="center">
      <img src="assets/step_response.png"><br><em>Figure 3: Closed-loop feedback performance achieving a critically damped response.</em> 
      
    </td>
    <td width="50%" align="center">
      <a href="https://youtube.com/shorts/9p_X2iOrVEk">
        <img src="assets/led_matrix_thumbnail.png">
      </a>
      <br><em>Video 2: LED Matrix firmware logic (Click to watch on YouTube).</em>
    </td>
  </tr>
</table>
    
---

##  Patent
**Certified Invention Patent Holder (Patent No. 1894025)**
*   **Design:** ESP32 + MPU6050 integration for motion telemetry.
*   **Logic:** Engineered deterministic 10ms-interval sampling loops for real-time velocity estimation and high-precision zero-crossing detection.
*   **Robustness:** Implemented state-based reset with hysteresis to reject noise during non-linear movement.

<table>
  <tr>
    <td width="33%" align="center">
      <img src="assets/Circuit_Design.png" alt="ESP32 to MPU6050 Interface">
      <br>
      <em>Figure 4: System Interconnect: I2C interface schematic between the ESP32 and MPU6050.</em>
    </td>
    <td width="33%" align="center">
      <img src="assets/Hardware_Architecture.png" alt="Breadboard Prototype">
      <br>
      <em>Figure 5: Populated physical PCB and directional signaling layout.</em>
    </td>
    <td width="33%" align="center">
      <img src="assets/baton_prototype.png" alt="Physical Prototype PCB Assembly">
      <br>
      <em>Figure 6: Breadboard prototype showcasing ESP32 and MPU6050 signal validation.</em>
    </td>
  </tr>
</table>

<p align="center">
  <a href="https://youtu.be/_PxIBCmbsC0">
    <img src="assets/patent_demo_thumbnail.png" width="600" alt="Watch Patent Device Demo Video">
  </a>
  <br>
  <em>Video 3: Performance demonstration of the motion-sensing signaling device. (Click to watch on YouTube)</em>
</p>

---

##  Education & Recent Activities

| Category | Details |
| :--- | :--- |
| **Education** | B.S. Electrical Engineering, UCR (Expected 2029) |
| **GPA** | 3.84 / 4.00 |
| **Leadership** | Incoming Projects Chair, IEEE UCR Branch |
| **Competitions** | All Ring Finalist, 1st Place (Robotics/Industrial Robot) |

---

###  Technical Proficiency
* **Programming:** C/C++, Python (PyTorch, OpenCV), MATLAB/Simulink, LabVIEW, ROS (Basic)
* **Engineering Tools:** Altium Designer, SolidWorks, Arduino, Visual Studio/VS Code
