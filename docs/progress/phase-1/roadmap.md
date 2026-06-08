
## Roadmap

_Document date: 8 June 2026_

### Next planned steps

The next phase of the project will add an **APDS-9930** ambient light sensor. It will be used to measure and log ambient brightness, giving the system an additional environmental signal that can be used in future features.

The sensor will also be used to **turn the display on and off and change pages** through touch interaction, making local control more practical and flexible.

### Home automation direction

The project will also be prepared for future integration with **Home Assistant** and other home automation systems, so lighting control can eventually be driven by ambient light data. This will create a path toward more intelligent automation and better coordination with the rest of the home.

### Network evolution

Another major goal is to extend network coverage through a combined **ESP-NOW + ESP-MESH** approach. Battery-powered nodes will continue to use **ESP-NOW** because it is lightweight and energy efficient, while mains-powered nodes will use **ESP-MESH** to help relay communication and allow distant or less reachable nodes to join the network.

This approach is meant to solve the problem of nodes that cannot always communicate directly because they are too far apart. In practice, mains-powered nodes will expand the network range, while battery nodes will stay optimized for low power consumption.

### Overall direction

In short, the roadmap focuses on three clear goals: ambient light handling, home automation integration, and a more extended network. The project is moving toward a system that is more intelligent, more practical, and better suited to real-world distributed deployments.
