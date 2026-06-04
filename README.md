# ESP32 Hybrid Sensor Network

🇮🇹 [Italian version](README.it.md)

This repository tells the story of a personal project that is still under development and was created to gain hands-on experience with ESP32, communication between nodes, local interfaces, and failover logic.

The basic idea is to build a modular IoT network made of one Master node and multiple Slave nodes, with ESP-NOW communication, automatic Master election in case of failure, support for battery or mains power, and the possibility of using different sensors depending on the application.

This is not presented as a finished product or a definitive professional solution. It is first and foremost a practical path of study, testing, and technical growth.

## Why this project

I have been following the IoT world for some time, but with this project I wanted to approach it in a more practical way, moving from curiosity and study to direct experimentation.

The goal is not just to build a sensor node, but a small distributed infrastructure that makes it possible to better understand real-world problems such as:

- coordination between nodes;
- service continuity in case of failure;
- limits of radio communication in real environments;
- local management through a web interface;
- differences between mains-powered nodes and battery-powered nodes.

At this stage the project is being tested on an **ESP32-C3 SuperMini** and, for the first trials, with environmental sensors such as the BMP280. The idea is to keep the structure flexible enough to reuse it with different sensors and therefore in very different application contexts.

## Project status

The project is **work in progress**.

The general structure is already defined, and the current code implements several important aspects: role management, a single Master node, automatic election, a local web interface, persistent parameters, status logging, and sensor integration.

At the same time, there are still parts in evolution. In particular, the practical tests are helping me understand more clearly where the current architecture works well and where it needs to be reconsidered.

## Current architecture

At the moment the system is based on two main roles:

- **Master node**: the only node that handles local coordination, hosts the Web Server, exposes the control GUI, and, when available, connects to the Wi-Fi network.
- **Slave nodes**: send status, sensor readings, and receive commands through ESP-NOW.

There is also an election logic in place. If the Master is no longer reachable, the other nodes can start an election and automatically promote a new Master according to the node priority and state.

This approach helps me reduce the load on the home Wi-Fi, because the local interface is concentrated on a single node while the others communicate over radio.

## What is already in the code

At this stage the project already includes:

- one active Master at a time;
- management of Master, Slave, and Candidate roles;
- automatic failover election;
- local Web GUI for monitoring and configuration;
- persistent parameter management;
- support for different power profiles;
- sensor data collection;
- per-node logging and state tracking;
- support for a local display.

This part is important to me because it is the foundation I am trying to build on while keeping the system simple.

## What I am learning from tests

One of the most interesting aspects of the project is that it is forcing me to deal with the real behavior of the network, not just the theoretical one.

In the tests I have done so far, I noticed that under certain practical conditions — for example node distance, walls, physical placement of the hardware, or the surrounding environment — keeping a single and stable view of the network can become more complicated than it seems at first.

I have observed that, in the current setup, if some nodes lose visibility of the Master, they may interpret the situation as a failure and start a new election. When that happens, the network can end up splitting into separate subnetworks, each with its own local reference point. This is one of the aspects I am working on the most, because it is showing me in a very concrete way where the current architecture needs to evolve.

## Planned evolution

These observations are what led to the next step of the project.

The idea I am working on is to evolve toward a more hybrid architecture: keep ESP-NOW, especially for lighter nodes or battery-powered nodes, and add a more robust communication structure for mains-powered nodes.

The direction is to introduce a mesh-style backbone among the powered nodes, so that coverage, continuity, and message routing can improve in more complex scenarios.

This part is not yet the core of the current implementation, and that should be stated clearly. At the moment it represents the next phase of the project: the point where I am trying to turn a system that works in the lab into something more solid even under less ideal conditions.

## Development approach

This project, including the documentation, was developed with the support of AI tools.

The use of AI does not replace the practical side of the work: testing, experiments, architectural choices, problem finding, requirement changes, and evaluating what works or does not work all depend on direct experience with the project. For me, AI is a support tool for development, refactoring, and documentation, not a substitute for experimentation.

## Development phases

### Phase 1 - First foundations

- Study of the general node structure.
- Definition of a single Master.
- Initial node-to-node communication via ESP-NOW.
- Early tests with sensors and status data.

### Phase 2 - Local control

- Introduction of the Web Server on the Master.
- Local dashboard for monitoring and control.
- Node configuration through the web interface.
- Management of labels, priorities, profiles, and operational commands.

### Phase 3 - System continuity

- Automatic election logic.
- Failover handling.
- Role changes when the Master is unavailable.
- Preference persistence and recovery after reboot.

### Phase 4 - Field verification

- Testing in environments that are less ideal than a workbench.
- Observation of the practical limits of radio coverage.
- Understanding the risk of network fragmentation.
- Need to rethink the communication model.

### Phase 5 - Architectural evolution

- Keep lightweight nodes using ESP-NOW.
- Introduce mains-powered nodes as a backbone.
- Improve robustness, coverage, and routing.
- Move toward a more reliable distributed network.

## Why I document it this way

At this stage I want the repository to explain the project clearly, even without showing everything as if it were already a finished solution.

I want people reading it to understand:

- what the system already does today;
- which practical problems have emerged;
- in which direction I am trying to evolve it;
- how much this journey is also a way for me to grow technically.

For that reason, at least initially, the repository is meant to contain mostly documentation, diagrams, images, development notes, and updates on the different phases of the work.

## What this repository can contain

To make the project clearer and easier to read, the most useful contents at this stage are:

- architecture description;
- diagrams of the node roles;
- photos of the real hardware;
- screenshots of the local GUI;
- project roadmap;
- progressive publication of the code.

## Roadmap

- [x] Definition of the single-Master architecture.
- [x] Basic node-to-node communication via ESP-NOW.
- [x] Local GUI and Web Server implementation.
- [x] Automatic Master election handling.
- [x] Sensor integration and persistent parameters.
- [ ] Improve robustness in larger environments.
- [ ] Introduce a mesh backbone for powered nodes.
- [ ] Refine the hybrid ESP-NOW + mesh model.
- [ ] Publish a more stable and complete version.

## Final note

This project is not meant to present a perfect solution, but to build practical experience on real IoT and embedded networking problems.

For that reason I want to document it honestly: not only by showing what already works, but also by showing what I am still trying to understand and improve.

## Credits

This project was developed by Ignazio Aita (https://github.com/igzat81/).
