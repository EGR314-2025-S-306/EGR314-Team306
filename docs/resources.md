# Resources

> The Resources section provides access to the project’s supporting materials and documentation for anyone who wants to delve deeper or replicate aspects of our design. Below is a summary of what is available:


#### 1. Hardware Design Files:
All PCB schematics and layout files (in KiCad format) for each subsystem board are provided. This includes the board for the HMI (keypad + OLED + ESP32), the Sensor suite board (with PIC MCU and sensor breakouts), the Actuator motor driver board (PIC MCU, motor drivers, regulators), and the Wi-Fi board (ESP32 module and regulator). These files allow others to examine our circuit design or even fabricate the boards. We also include 3D CAD models (in STEP format) of the mechanical assembly – showing how the solar panel is mounted and how the enclosures for sensors and electronics were arranged.

**Download Hardware Files:**
- 🔗 [Internet Communication Subsystem PCB & Schematic (Kushagra)](./subfolder/EGR314_KD_Subsystem.zip)  
- 🔗 [Sensor Suite Hardware Files (Ian)](./subfolder/Ian_Sensor_PCB.zip)  
- 🔗 [HMI PCB and OLED Mount (Aarshon)](./subfolder/Aarshon_HMI_PCB.zip)  
- 🔗 [Actuator Driver Board Design (Alex)](./subfolder/Alex_Actuator_Board.zip)  
- 🔗 [3D Printed Mechanical Assembly (STEP Models)](./subfolder/Team_Assembly_CAD.zip)

#### 2. Firmware Source Code:
The complete source code for each microcontroller is available for download. This includes MPLAB X projects in C for the PIC18F27Q10 and PIC18F47Q10 subsystems, as well as the ESP32 code. The ESP32 code (for both HMI and Wi-Fi units) is written in MicroPython and organized into modules for sensor parsing, MQTT communication, and display updates. We provide a .zip file containing the Wi-Fi subsystem’s ESP32 firmware (for MQTT publishing), and similarly packaged code for the HMI ESP32 and PIC firmware hex files. Instructions are included for how to flash the code onto the respective devices, so that others can reproduce our setup. 

**Download Individual Firmware Projects:**
- 🔗 [Kushagra Dashora – Internet Communication](./subfolder/KD_Subsystem_Code.zip)  
- 🔗 [Ian – Sensor Suite](./subfolder/Ian_PIC_Sensor_Code.zip)  
- 🔗 [Aarshon – Human Machine Interface](./subfolder/Aarshon_ESP32_HMI_Code.zip)  
- 🔗 [Alex – Actuator System](./subfolder/Alex_PIC_Control_Code.zip)

  
#### 3. Demo Video:
We have included a short video clip demonstrating the weather station in action. In the video, a user cycles through different data views on the HMI screen, and the solar panel automatically adjusts as a flashlight is moved around it (simulating the sun). This video is meant to provide a quick overview for those who couldn’t see the project in person. It’s linked via YouTube for easy streaming. 

** attach all of them here with labels **

#### 4. Project Report and Diagrams:
In addition to the content on this site, we offer a compiled PDF report containing our entire documentation (proposal, design report, and final paper). All important diagrams – such as the system block diagram, process flow (sequence diagram), and message structure table – are included in the report. This is useful for educators or students who want to study the project offline. We also include our team’s presentation slides used in our final presentation, which concisely summarize the project’s results and lessons. 

** attach all of them here with labels **

#### 5. Innovation Showcase Highlights

To celebrate the end of our semester-long work, here are some photos from the Innovation Showcase event, where we demoed our live system to industry professionals, faculty, and fellow students.

| Working Subsystem | Team at Showcase |
|-------------------|------------------|
| ![Subsystem Demo](./subfolder/Subsystem_Demo_Photo.jpg) | ![Team Photo](./subfolder/Showcase_Team_Photo.jpg) |

---

***By making these resources available, we hope to encourage further learning and possibly inspire future projects. Whether it’s reusing our code for an IoT dashboard or examining our circuit for the solar tracker, these files are there to help others build upon what we learned.***
