# HelmingSense: A Yacht Sensor & Automation System

HelmingSense is an open, modular, and extensible system for real-time sensor monitoring, automation, and logging on yachts and sailing vessels. It is designed for robust operation, easy expansion, and maximum automation—especially for single-handed sailors and those seeking deep insights into their vessel’s health and performance.

Learn more at [HelmingSense.com](https://HelmingSense.com)

## Vision

- **Distributed sensors:** ESP32/Arduino nodes throughout the yacht, reporting via WiFi (MQTT, HTTP, Signal K) to a central server.
- **Signal K backbone:** All data funneled into Signal K for integration, logging, visualization, and sharing.
- **Automated logbook:** Automatic narrative/tabular event logging, with crew/skipper annotation and voice input options.
- **Custom dashboards:** KIP console and/or bespoke interfaces for navigation, status, and analytics.
- **Open hardware & software:** Fully documented, reproducible, and extensible by the community.

## Features

- Modular ESP32 sensor firmware (voltage, temp, flow, IMU, etc.)
- Signal K integration and plugin development
- Automated logbook system with event/narrative generation
- Node-RED flows for automation and alerting
- Documentation for hardware, software, and wiring
- Open-source licensing for community use and contribution

## Repository Structure

```
helmingsense/
├── docs/                   # Project documentation, guides, diagrams
├── hardware/               # Schematics, PCB designs, enclosure files
├── firmware/               # ESP32/Arduino sensor code
├── server/                 # Mini PC/server setup, Signal K plugins, Docker, scripts
├── logbook/                # Logbook automation code and data schemas
├── dashboard/              # KIP console configs, custom UI code
├── tests/                  # Unit and integration tests
├── examples/               # Example configs, sample data, quickstart sketches
├── .github/                # GitHub workflows, issue/PR templates, contributing guide
├── LICENSE
├── README.md
└── CONTRIBUTING.md
```

## Getting Started

1. **Read the [docs/overview.md](docs/overview.md)** for system architecture and setup flow.
2. **Choose your first module:**  
   - ESP32 sensor node ([firmware/](firmware/))
   - Signal K server/extensions ([server/](server/))
   - Logbook automation ([logbook/](logbook/))
3. **Contribute!** See [CONTRIBUTING.md](CONTRIBUTING.md) for how to get involved.

## Documentation

- [System Overview](docs/overview.md)
- [Hardware & Wiring](docs/hardware.md)
- [Sensor Firmware](docs/firmware.md)
- [Server Configuration](docs/server.md)
- [Logbook Automation](docs/logbook.md)
- [Custom Dashboards](docs/dashboard.md)
- [Troubleshooting](docs/troubleshooting.md)

## License

This project is licensed under the [CC BY-NC 4.0](LICENSE) license.  
Commercial use is not permitted without explicit permission.

## Acknowledgements

This project builds on the ideas and work of the open marine electronics community, including Signal K, OpenPlotter, Bareboat Necessities, and countless sailors and developers.

---

**Fair winds and following seas!**
