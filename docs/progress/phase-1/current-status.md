# Current status

_Data of the document: 8 June 2026_

## Note on the date

The date refers to the project status described in this document and identifies the level of progress reached at the time of writing.

## Code availability

At this stage, the code is not yet being published. This document therefore describes the architecture, design choices, and prototyping status in technical terms, but it does not represent a release of the firmware or the source base used during development.

## Progress reached in phase 1

By the end of phase 1, a working prototype was built around **ESP32-C3 SuperMini** nodes, designed for distributed monitoring and for controlling the nodes through a centralized web interface.

Each node was designed to dynamically assume one of three logical roles:

- **Master**, when the node coordinates the system, serves the web GUI, maintains the global node view, and, when available, also connects to the local Wi-Fi network.
- **Slave**, when the node operates as a network peripheral, sends its state and sensor data, and receives remote commands.
- **Candidate**, when the node participates in the Master election mechanism in the absence of a valid Master or after a failover.

The system was therefore not built as a simple point-to-point connection, but as a concrete base for a distributed network with coordination logic, operational continuity, and room for future expansion.

## Technical architecture

The architecture implemented in this phase is based on a network of ESP32-C3 SuperMini nodes that communicate mainly through **ESP-NOW**, a protocol chosen for direct low-latency communication without requiring a traditional Wi-Fi infrastructure between nodes.

In this configuration, ESP-NOW is used for:

- broadcasting node status;
- sending periodic heartbeat and status packets;
- transmitting local telemetry;
- propagating remote commands to the nodes;
- managing the Master election mechanism.

The Master node represents the logical coordination point of the system. When a node becomes Master, it enables the web GUI, starts a local access point, and also attempts to connect to the home Wi-Fi network in order to provide GUI access through the LAN and, when available, through mDNS.

The Slave nodes, on the other hand, remain focused on sensor reading, display updates, status transmission, and reception of remote commands.

## Heartbeat mechanism

This phase already includes a real **heartbeat** mechanism, which is essential to determine whether the Master is still operational and to keep the network state aligned. The Master sends periodic heartbeats at a dedicated interval, while the other nodes also send status packets at separate intervals.

In practice, when a node is acting as Master, it periodically transmits a heartbeat packet containing at least its role, node identifier, radio channel, uptime, operational state, and any available telemetry values.

When a Slave or Candidate receives a valid heartbeat from a non-standby Master, it updates the timestamp of the last detected Master and therefore considers that coordinator still valid. This prevents unnecessary elections as long as the Master continues to be detected regularly.

If no valid heartbeat is received from the Master for a period longer than the expected timeout, the node considers the Master unavailable, starts a verification procedure, and, if needed, enters the election mechanism.

In other words, the heartbeat is the periodic signal by which the Master proves it is still active; its prolonged absence is what triggers failover.

## Master election

An **election/failover** mechanism has been implemented: at startup, or when the Master is no longer detected within the expected timeout, nodes can start an election procedure based on priority, channel, and the **time validity of the information received** about active nodes.

The firmware compares the local priority with the priorities of the observed remote nodes and, when priorities are equal, also uses the node identifier to determine the best candidate. The GUI also allows a global election to be forced or a specific node to be promoted to Master through a remote command.

## Sensor used

For this phase, the available sensor in the prototype was used, namely a **BMP280**, employed to acquire **temperature** and **barometric pressure**.

The collected measurements are:

- acquired periodically by the node firmware;
- stored in the node runtime state;
- published in the status packets sent via ESP-NOW;
- displayed on the local display;
- shown in the Master GUI;
- stored in memory as a history and exported as CSV from the sensor section.

This choice made it possible to validate the full technical chain: physical data acquisition, network propagation, local visualization, remote visualization, and history storage.

## Adaptation to two displays

During this phase, **two different displays** were available, and the code was adapted to support both without duplicating the application logic. In particular, the display subsystem manages an **SSD1306 OLED** over I2C and a **ST7789 TFT** over SPI.

Display detection happens during initialization: the firmware first tries to detect an OLED at the expected I2C addresses and, if no response is found, initializes the TFT. In this way, the node reuses the same state logic while using different rendering depending on the actual hardware present.

This choice shows that, already in phase 1, the firmware was not written rigidly for a single display, but with an initial abstraction of the display subsystem to make the project more reusable and easier to evolve.

## Modular software structure

One of the strongest results achieved in this phase is the division of the code into multiple files with distinct responsibilities. The project is not organized as a single monolithic sketch, but as a modular structure that separates network logic, sensors, GUI, persistence, and local rendering.

### Main files and responsibilities

- **`node.ino`**: main firmware file; initializes Wi-Fi, ESP-NOW, boot logic, role management, Master election, packet send/receive handling, the main loop, and overall orchestration.
- **`config.h`**: collects configuration constants, pin mappings, timeouts, default parameters, radio channel, network settings, and shared data structures.
- **`sensors.h`**: handles sensor initialization and reading of the available physical values.
- **`sensor_log.h`**: maintains the sensor reading buffer and the historical logging logic.
- **`sensor_display.h`**: abstracts local display handling, supporting both OLED and TFT and enabling different informational pages.
- **`gui.h`**: contains the main web GUI, API endpoints, node snapshot construction, and remote control logic from the browser.
- **`gui_sensors.h`**: extends the GUI with the sensor section, history, charts, reading registration, and CSV export.

This modular organization is an important result of phase 1 because it makes the project more readable, easier to maintain, and already prepared for future expansion.

## Communication protocol

The radio protocol actually used between nodes in this phase is **ESP-NOW**. At the application level, the project implements a proprietary protocol on top of ESP-NOW based on structured packets that transmit the sender ID, optional command target, node role, radio channel, priority, display state, standby state, logging state, uptime, timestamp, temperature, pressure, and other operational metadata.

Several packet types are also defined to cover the system’s main needs:

- **heartbeat** packets;
- **status** packets;
- **command** packets for remote control;
- **election** packets for Master re-election.

This means that, although still prototypical, the project has already gone beyond simple telemetry and now has a concrete protocol base for distributed coordination and remote node control.

## Persistence and configurable parameters

The firmware saves several parameters in non-volatile memory through **Preferences / NVS**, so the configuration is preserved after reboot. In this phase, among the stored values are node priority, radio channel, label, display state, logging enable, display page, sampling interval, maximum number of records, hostname, and offline timeout.

This is important because it moves the project from a temporary demo to an experimental platform that already includes persistent configurability.

## Breadboard prototype hardware

From a hardware perspective, phase 1 is still clearly a **breadboard prototype** phase. The system has not yet been moved to a dedicated PCB or a final enclosure; the nodes were built as bench prototypes useful for checking wiring, module integration, displays, power supply, and overall firmware behavior.

This should be stated explicitly because it correctly defines the maturity level of the project: the software and network logic have already reached a good level of complexity, but the hardware is still in a lab-stage form.

The attached images document this prototypical breadboard condition of the nodes:

- `images/node_prototype_master_temp_press.jpg`
- `images/node_prototype_slave_temp_press.jpg`

## Web GUI

The GUI built in this phase is already one of the most advanced elements of the project. It is not just a debug page, but a real operational interface accessible from the Master, designed to monitor node status and configure individual devices.

### 1. Overview

The overview page shows a compact but rich view of the system state. It includes information such as the Master mDNS host, the Master LAN IP, the Access Point IP, the AP SSID, the node role, power type, radio channel, Wi-Fi status, sensor status, number of connected nodes, and a complete table of known nodes.

The node table already includes quick controls directly embedded in each row, for example to enable or disable the display, put a node into standby, change the display page, enable or disable logging, force promotion to Master, open node configuration, or reboot the node.

Reference image:

- `images/overview_gui_phase1_GUI.jpg`

### 2. Node configuration

The configuration section makes it possible to modify the operating parameters of the selected node. In particular, from the GUI it is possible to change the node name, priority, radio parameters, power type, bridge association, and also send remote operating commands and consult the node log.

To select the node to edit, the GUI does not use the numeric node ID directly as the editing parameter exposed to the interface. Instead, it generates a **temporary token internally associated with that node**, that is, a string used in the GUI calls to open the correct detail view and send commands to the right target.

In practice, this token is an application-level identifier used by the GUI as a working key: the browser passes the token, while the firmware internally converts it back into the node’s real ID. This choice makes the interface logic cleaner and better separates internal node identification from GUI action handling.

Reference image:

- `images/configure_node_phase1_GUI.jpg`

### 3. Sensor section

The GUI also includes a dedicated sensor section, which is already quite complete for phase 1. This part allows the user to select the node to monitor, view immediate KPI values for temperature and pressure, consult the number of stored readings, know the sampling interval, observe the reading trend on a chart, change sampling parameters, enable or disable manual recording, delete history or recordings, and export the data in CSV format.

From a design point of view, this section shows that the work did not stop at displaying only the latest value read, but already introduced history, explicit recording, and an initial logging base consultable through the web interface.

Reference image:

- `images/bmp280_sensor_phase1_GUI.jpg`

## Overall assessment of phase 1

Phase 1 can be considered completed with a technically solid result. In particular, the following have already been validated: radio communication between nodes, the operational distinction between Master, Slave, and Candidate, the heartbeat mechanism, failover with Master election, real temperature and pressure acquisition through the BMP280, firmware adaptation to two different displays, code modularization into multiple components, persistence of configuration parameters, and the presence of an advanced GUI for monitoring and control.

At the same time, the system should still be considered a prototype: the hardware is still on a breadboard, robustness will need to be further verified through longer tests, and the code is not yet being distributed in this phase. However, for a phase 1 milestone, the project has already reached a very solid and well-structured technical base that can serve as the foundation for later developments.
