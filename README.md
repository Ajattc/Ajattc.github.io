# MCO Airport Tram Controller - Real-Time Systems

## Project Overview
This is a themed theoretical application for my Real Time Systems class. The project connects RTOS to industrial motor control drivers found in the Orlando MCO Airport's Trams. This is not fully fleshed out with realistic data, but more of an exercise of designing deadlines and real time communications between ISR's and tasks.

### Theme
An industrial system that aims to control drivers and sensors, built to demonstrate real-time emergency and safety driver operations for a Control Engineer role.

### Background
The Orlando MCO Airport Tram system transports passengers from the main airport building to the various terminals where they board the plane. These trams travel back and forth autonomously (except for some overrides) and employ certain features used in real time systems. These function include: motor driver operations, sensor data processing, control loops, and various other safety and emergency operations. I chose this because I've been on these trams before and found them interesting and a simple real-time system which connects to my Real Time Systems Class. 

My final project builds off of Application 4 and coordination of Interrupt Service Routines and primitive handling for data in real time systems.

---

## System Architecture
*(Upload your architecture diagram image to your GitHub repository and replace `diagram.png` with your actual file name)*

![System Architecture Diagram](./diagram.png)

---

## Demo Video
*(To embed a YouTube video, replace `YOUR_VIDEO_ID` with the actual ID from your YouTube link. If using Wokwi, you can paste the Wokwi embed code here instead.)*

<iframe width="560" height="315" src="https://www.youtube.com/embed/YOUR_VIDEO_ID" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---

## Task Table & WCET Evidence

This project has 4 main tasks to simulate regular operation, listed in terms of decreasing priority: a vital control loop, sensor reading task, motor control task, and logging task. The tasks have simulated deadlines and periods as follows:

| Task | Function | Period (ms) | WCET measured (µs) | WCET + 30% margin (µs) | Deadline | Priority | Core |
|---|---|---:|---:|---:|---:|---:|---:|
| A | Control_loop | 10 | 294 | 382 | 3 ms | 15 | 1 |
| B | Blink/Sensor Read | 50 | 109 | 142 | 10 ms | 11 | 1 |
| C | Motor_control | 25 | 1,033 | 1,343 | 10 ms | 8 | 1 |
| D | Logging | 200 | 27,679 | 35,983 | 50 ms | 4 | 1 |


### Other Tasks and ISRs
Along with these tasks, there is a web server that serves to log important data like Worst Case Execution times and iterations of each of the 4 tasks. The webserver runs of core 0 to prevent interference with vital system operation. 

There is an accompanying button ISR for initiating an emergency stop(red) using a task notification and collecting a VFD data sample(black) using a counting semaphore. Each has their own bottom half ISR that is blocked until the semaphore is given.

---

## Hazard Analysis & Industry Standard Mapping
*(Below is a template table for your hazard analysis. Fill in the specific hazards related to your motor controllers and emergency stops, along with any standards your professor requires, such as IEC 61508 or DO-178C)*

| Hazard | Potential Cause | Severity | RTOS Mitigation Strategy | Standard Mapping |
|--------|-----------------|----------|--------------------------|------------------|
| Task A Deadline Miss | High Priority Starvation | Critical | Strict priority assignments | IEC 61508 SIL 3 |
| E-Stop Failure | Missed Button Interrupt | Critical | Dedicated immediate ISR | ... |
| Sensor Disconnect | Hardware Failure | Moderate | Timeout handling in Task B | ... |
