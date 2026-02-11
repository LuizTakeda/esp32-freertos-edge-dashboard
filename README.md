# FreeRTOS + ESP32: Embedded Systems Programming

This repository contains the source code for an academic extension project on embedded systems using **FreeRTOS** and **ESP32**.

The project demonstrates core RTOS concepts such as multitasking, real-time scheduling, and inter-task communication. The final output is a web dashboard hosted directly on the ESP32, enabling real-time monitoring and control through an asynchronous architecture.

Part of [*The Parallel Computing School: Short Courses and Tutorials*](https://github.com/rogerioag/ecp-minicursos).

## Tech Stack

**Firmware:**  
[![C](https://img.shields.io/badge/Language-C-blue?logo=c)](https://www.w3schools.com/c/c_intro.php)
[![ESP32](https://img.shields.io/badge/Microcontroller-ESP32-red?logo=espressif)](https://www.espressif.com/en/products/socs/esp32)
[![ESP-IDF](https://img.shields.io/badge/Framework-ESP--IDF-red?logo=espressif)](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/)

**Frontend:**  
[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![SASS](https://img.shields.io/badge/SASS-CC6699?logo=sass&logoColor=white)](https://sass-lang.com/)
[![Webpack](https://img.shields.io/badge/Webpack-8DD6F9?logo=webpack&logoColor=white)](https://webpack.js.org/)
[![Web Components](https://img.shields.io/badge/Web%20Components-native-blue)](https://developer.mozilla.org/en-US/docs/Web/API/Web_components)
[![Node.js](https://img.shields.io/badge/Node.js-339933?logo=node.js&logoColor=white)](https://nodejs.org/)
[![Express](https://img.shields.io/badge/Express.js-000000?logo=express&logoColor=white)](https://expressjs.com/)

The dashboard is built with [Web Components](https://developer.mozilla.org/en-US/docs/Web/API/Web_components) and [Webpack](https://webpack.js.org/) to bundle and minify the project, making it ideal for resource-limited devices like the ESP32.

For more details, see the [Frontend README](./components/web_server/frontend/README.md)

## Hardware Schematic

The wiring and electronic components can be reviewed in the following schematic:

<img width="1920" alt="Hardware Schematic" src="/hardware/ESP32 FreeRTOS Edge Dashboard Schema.svg" />

<img width="1920" alt="Hardware Schematic" src="https://github.com/user-attachments/assets/cd5e0863-8ba5-4f63-b93b-11b763d8a582" />

## Architecture

The system utilizes an **Event-Driven Architecture (EDA)** to maximize responsiveness and minimize resource consumption.

- **Real-Time Monitoring**: Instead of traditional HTTP polling, this project implements **Server-Sent Events (SSE)**. This allows the ESP32 to push sensor updates to the frontend instantly as they happen, significantly reducing network traffic and latency.
- **Non-Blocking Hardware Abstraction**: Each hardware peripheral (Analog, Digital, Sensors) operates in its own dedicated FreeRTOS task. They communicate with the Web Server via asynchronous events, ensuring that a slow sensor read never freezes the UI or the WiFi stack.

```mermaid
flowchart TB
    %% Extern entity
    User((User/Browser)) <-->|HTTP/SSE| Web_Server 

    subgraph ESP32_Firmware [ESP32 Application]
        direction LR

        subgraph ESPIDF [System Services]
            direction TB
            WiFi[WiFi Driver] --> EventLoop[Default Event Loop]
        end

        subgraph Web_Server [Web Server]
            direction TB
            WBURIHandler[URI Handlers]
            WBEvent[Event Handlers]
            WBSSE[SSE Task]
            WBEvent --> WBSSE 
        end

        subgraph Hardware_Drivers [Hardware Abstraction Layer]
            direction TB
            
            subgraph Inputs [Monitoring]
                direction LR
                Analog_Input[Analog Input Task]
                Digital_Input[Digital Input Task]
                Sensor_Block[Sensor Task]
            end

            DigitalOutput[Digital Output]
        end

        %% Connections
        EventLoop --"IP/WIFI Events"--> Web_Server
        Inputs --"Events"--> Web_Server
        Web_Server <--"Control"--> DigitalOutput
    end

    %% Styling
    style Web_Server stroke:#01579b,stroke-width:2px
    style Hardware_Drivers stroke:#33691e
    style ESPIDF stroke:#e65100
```

## Build and Flash

The project is configured to handle the frontend assets automatically during the firmware compilation process.

 1. **Prerequisites**: Ensure you have the ESP-IDF SDK installed and configured.

 2. **Compilation**: Run the following command in the root directory:
    ```bash
    idf.py build
    ```
    > This will trigger the Webpack build for the frontend and embed the resulting files into the firmware.

 3. **Flash**:
    ```bash
     idf.py -p [PORT] flash monitor
    ```

## Demonstration

Once the device is flashed, monitor the serial output to find the assigned IP address. Open your browser and navigate to that IP.

### Digital Input & Control

Monitor button states and control digital outputs in real-time. 

<video src="https://github.com/user-attachments/assets/47d56d9a-5213-448e-a3d6-8f0649e0f05e" width="1920" controls autoplay loop muted>
  Seu navegador não suporta o player de vídeo.
</video>



### Analog Monitoring

Visualize ADC values and sensor data through dynamic charts. 

<img width="1920" alt="Analog Input Dashboard" src="https://github.com/user-attachments/assets/94fded25-5059-4fc1-a0be-00b0b86bcaae" />

### Multiple clients

The system is designed to handle multiple concurrent clients using SSE broadcasting.

<img width="1920" alt="Analog Input Dashboard" src="https://github.com/user-attachments/assets/94fded25-5059-4fc1-a0be-00b0b86bcaae" />


