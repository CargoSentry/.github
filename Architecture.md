# Overview

Cargo Sentry connects ultra‑low‑power trailer hardware to an edge AI module and a .NET cloud backend, delivering a visual chain of custody with wake‑on‑event efficiency. The architecture is modular so hardware, edge inference, and cloud services can be developed, tested, and scaled independently.

```mermaid
  ---
config:
  theme: neutral
  look: neo
  layout: elk
---
flowchart TB
 subgraph Trailer_Device["Trailer_Device"]
        B["Low-Power Sensors"]
        A["Arduino UNO Q MCU"]
        C["Edge AI Module"]
        D["Ceiling Camera + IR Flood"]
        E["Edge AI Model"]
        F["Local Encrypted Buffer"]
        G["Cellular Modem"]
  end
 subgraph Cloud_Backend["Cloud_Backend"]
        H["IoT Ingest Gateway (.NET)"]
        I["Event Processor (.NET)"]
        J["Object Storage (images)"]
        K["Relational DB (events, metadata)"]
        L["Notification Service"]
        M["Claims API"]
  end
 subgraph Frontend["Frontend"]
        N["App Lab Prototype"]
        O["Web Dashboard (.NET Blazor)"]
        P["Mobile App"]
  end
 subgraph Integrations["Integrations"]
        Q["Insurance Portal"]
        R["Fleet Management"]
  end
    A -- I2C / SPI --> B
    B -- Interrupt --> A
    A -- UART --> C
    C -- Camera Trigger --> D
    C -- Local Inference --> E
    C -- Store --> F
    C -- Cellular IoT --> G
    G -- MQTT/HTTPS --> H
    H --> I
    I --> J & K & L & M
    N -- REST/Webhook --> H
    O -- API --> I
    P -- Push --> L
    M --> Q & R
```
Purpose: detect meaningful events with minimal power and wake the edge module only when necessary.

    Sensors remain in ultra‑low‑power standby; interrupts wake the MCU.

    MCU validates trigger patterns (debounce, signature) before waking the edge module.

    Camera and IR must power on within ~50 ms to capture the event window.

