# The Knowledge Network Game

## Conference Room Demo for the Knowledge Engine

A serious game for 15-30 participants that turns a conference room into a
living, breathing Knowledge Engine network -- bridging the physical and digital
in a way no slide deck ever could.

---

## Core idea

Every table in the room is a **Knowledge Base**. Each table has physical
sensors, actuators, and a glowing "node lantern." When knowledge flows between
tables, **physical light tubes connecting them pulse with colour**. The
audience doesn't just hear about semantic interoperability -- they *see* it
ripple across the room as coloured light, and they *cause* it by pressing
buttons, turning dials, and playing cards.

---

## Setting: "District 2030"

The conference room represents a small neighbourhood in 2030. Each table is a
different stakeholder in a net-zero-energy district:

| Table | Role | Physical devices | KE interactions |
|-------|------|-----------------|-----------------|
| **Solar Farm** | Energy producer | Light sensor (torch = sun), dial for panel angle | ANSWER: energy production; POST: production forecast |
| **Heat Pump** | HVAC provider | Fan (physical breeze!), temperature dial | ANSWER: heating capacity; REACT: comfort requests |
| **Smart Home A** | Residential occupant | Temperature sensor, comfort button (red/green) | ASK: current comfort; POST: comfort preference |
| **Smart Home B** | Residential occupant | Occupancy button (present/away), LED strip | ASK: energy price; POST: occupancy status |
| **Energy Market** | Price signal provider | Physical price ticker (servo-driven flip display) | ANSWER: energy price; POST: price updates |
| **Grid Operator** | Balancing authority | Big red "blackout" button, capacity gauge | ASK: district balance; REACT: overload alerts |
| **Building Manager** | Facility manager (optional) | Tablet dashboard | ASK: all comfort + energy; POST: control setpoints |

Each table runs a Raspberry Pi (or laptop) with a small Python Knowledge Base
using the KE REST API.

---

## The physical network

### Node lanterns

Each table has a frosted glass lantern (or 3D-printed orb) containing an
RGB LED strip controlled by the local Pi. The lantern shows:

- **Colour**: the type of knowledge this node primarily deals with
  (orange = energy, blue = comfort, green = price, white = grid)
- **Pulse**: a heartbeat that speeds up when the node is actively exchanging
- **Brightness**: dims when the node goes offline

### Connection tubes

Between the tables, semi-transparent acrylic tubes (or LED strip channels) run
along the table edges and across the floor. Each tube contains an addressable
LED strip (WS2812B). When data flows from Table A to Table B:

1. A coloured pulse travels from A's lantern, through the tube, to B's lantern
2. The colour encodes the type: orange = energy data, blue = comfort,
   green = price, purple = reasoner-inferred chain
3. The speed encodes urgency (fast = alert, slow = routine)

A central Raspberry Pi ("the network visualiser") subscribes (via REACT) to a
special **observation Knowledge Base** that monitors all exchanges and drives
the LED animations.

### Central screen

A projector shows the KE admin-ui (or a custom D3.js visualisation) with the
live network graph: nodes, registered interactions, and data flowing. This
mirrors what the physical lights show, connecting the tangible to the digital.

---

## Game flow

### Act 1 -- "Boot the District" (5 min)

Tables power on one by one. As each Pi registers its Smart Connector with the
Knowledge Directory, its lantern lights up and the connection tubes to
neighbours awaken. The audience *sees* the network forming.

**KE feature showcased: dynamic discovery.** No table was told who the others
are. The Knowledge Directory lets them find each other.

### Act 2 -- "A Normal Day" (10 min)

The game master narrates: *"It's a sunny Tuesday. Solar Farm, point your torch
at the light sensor!"*

A participant shines a torch (representing sunshine) at the Solar Farm's light
sensor. The Solar Farm KB POSTs production data. The connection tubes light up
as the Grid Operator's REACT picks it up, and the central display updates.

Meanwhile, Smart Home A presses the "I'm cold" button (comfort request). Their
KB POSTs a comfort preference. The Heat Pump's REACT fires. The fan on the
Heat Pump table starts blowing -- actual warm air from a small heater fan.

**KE features showcased: POST/REACT pub-sub, semantic matching on SAREF
ontology patterns.**

### Act 3 -- "The Price Spike" (10 min)

The Energy Market table turns a dial. The physical price ticker flips to a high
number. The Energy Market KB POSTs a price update. Multiple REACT handlers
fire:

- Smart Home B's LED strip turns red (expensive!)
- Smart Home A is asked by the Building Manager: "Can you shift your heating
  to later?" -- an ASK routed *through the reasoner* because the Building
  Manager doesn't directly know about comfort preferences but the KE chains
  Smart Home A (comfort) + Energy Market (price) + Heat Pump (schedule) to
  construct an answer.

The physical tubes show a **purple pulse** (reasoner chain) bouncing from the
Building Manager, to Smart Home A, to Energy Market, to Heat Pump, and back.
The audience sees the multi-hop reasoning as a physical wave of light.

**KE features showcased: reasoning/orchestration (the "killer feature"),
automatic chaining of knowledge bases, SAREF ontology alignment.**

### Act 4 -- "Node Failure" (5 min)

The game master unplugs the Solar Farm's Pi. Its lantern dims. The Grid
Operator's next ASK for district balance returns partial results -- the
audience sees which tubes go dark. Then a new table ("Battery Storage") plugs
in. Its lantern lights up, connection tubes awaken, and the Grid Operator's
next ASK automatically includes the new node.

**KE features showcased: resilience, graceful degradation, dynamic
participation -- no reconfiguration needed.**

### Act 5 -- "The Blackout Challenge" (10 min, competitive)

The Grid Operator announces: total demand exceeds supply. Each table must
negotiate. The game master gives each table a **challenge card**:

- Solar Farm: "Cloud cover! Production drops 50%." (cover half the sensor)
- Smart Home A: "Guest arriving -- comfort is non-negotiable!"
- Smart Home B: "Willing to shift to battery if price is right."
- Heat Pump: "Can pre-heat now to coast later."
- Battery Storage: "Full charge, will sell at 2x market price."

Tables must use their KE interactions (ASK/ANSWER/POST/REACT) to coordinate.
The physical lights show the negotiation traffic. The Grid Operator has a
physical gauge showing supply vs demand. The goal: bring the gauge back to
balance without hitting the big red "blackout" button.

**KE features showcased: complex multi-party orchestration, the entire
interaction model working together under pressure, data staying at the source
(each table keeps its own state).**

---

## What the audience takes away

| Message | How the game shows it |
|---------|-----------------------|
| KE enables interoperability without central control | No table has a "master database" -- they each keep their own data |
| Semantic matching means you don't need to agree on APIs | Tables wrote their graph patterns independently, KE matched them |
| The reasoner composes knowledge chains automatically | The purple pulse shows multi-hop inference nobody had to wire up |
| New participants join seamlessly | Battery Storage joined mid-game, zero config |
| Failure is graceful | Solar Farm went down, the network adapted |
| It works with real IoT hardware | Physical sensors, actuators, real SAREF patterns |

---

## Bill of materials (per table)

| Item | Approx. cost | Purpose |
|------|-------------|---------|
| Raspberry Pi 4 (or laptop) | EUR 50 | Runs the KB Python script + drives LEDs |
| WS2812B LED strip (1m) | EUR 8 | Node lantern |
| Frosted glass jar / 3D printed orb | EUR 5 | Lantern housing |
| DHT22 temperature/humidity sensor | EUR 5 | Physical sensing |
| LDR light sensor module | EUR 3 | Solar Farm: detect torch light |
| Push buttons (x2) | EUR 2 | Comfort / occupancy / alerts |
| Small USB fan | EUR 10 | Heat Pump: physical airflow |
| Potentiometer / rotary encoder | EUR 3 | Dials for price, panel angle |
| Jumper wires, breadboard | EUR 5 | Wiring |

Per-table cost: ~EUR 90 (with Pi) or ~EUR 40 (using participants' laptops)

### Shared infrastructure

| Item | Approx. cost | Purpose |
|------|-------------|---------|
| WS2812B LED strips (10m total) | EUR 30 | Connection tubes between tables |
| Semi-transparent cable channels | EUR 20 | Housing for connection-tube LEDs |
| Raspberry Pi (network visualiser) | EUR 50 | Drives connection-tube LEDs |
| Projector + screen (usually available) | -- | Central network visualisation |
| Wi-Fi router | EUR 30 | Local network for KE |
| Servo-driven flip display (optional) | EUR 40 | Energy Market price ticker |

**Total for a 6-table setup: ~EUR 700-900**

---

## Technical architecture

```
  [ Wi-Fi Router ]
        |
   +---------+------ ... ------+
   |         |                 |
 [Pi/Laptop] [Pi/Laptop]   [Pi/Laptop]     <- one per table
  runs:       runs:          runs:
  - KE REST   - KE REST      - KE REST
    client      client         client
  - sensor     - sensor       - sensor
    driver      driver         driver
  - LED        - LED          - LED
    driver      driver         driver
        |         |                 |
   [local GPIO]  [local GPIO]  [local GPIO]
   sensors+LEDs  sensors+LEDs  sensors+LEDs

        +------ KE Runtime ------+
        | (runs on a central Pi  |
        |  or beefy laptop)      |
        |                        |
        | - Knowledge Directory  |
        | - Smart Connectors     |
        | - Reasoner             |
        | - Admin UI             |
        +------------------------+

        +-- Network Visualiser --+
        | (dedicated Pi)         |
        | - REACT KB subscribed  |
        |   to all POST topics   |
        | - Drives connection    |
        |   tube LEDs via GPIO   |
        +------------------------+
```

### Software components to build

1. **Table KB scripts** (Python, ~200 lines each)
   - Register KB + interactions via REST API
   - Read sensor via GPIO
   - Drive lantern LEDs via GPIO
   - Handle ASK/ANSWER/POST/REACT long-polling

2. **Network Visualiser KB** (Python, ~300 lines)
   - Registers REACT interactions matching all domain graph patterns
   - On each REACT, identifies source + target tables
   - Sends LED animation commands to connection-tube LED strips

3. **Central dashboard** (optional, extends existing admin-ui)
   - Real-time D3.js graph visualisation
   - Shows knowledge flow, interaction types, binding sets

4. **Game master console** (simple web UI)
   - Trigger scenario events (price spike, cloud cover, blackout)
   - Control game pacing

### SAREF graph patterns used

```sparql
# Energy production (Solar Farm)
?production rdf:type saref:Measurement .
?production saref:relatesToProperty saref4ener:Power .
?production saref:hasValue ?watts .
?production saref:isMeasuredIn saref:Watt .

# Comfort preference (Smart Home)
?preference rdf:type saref:Command .
?preference saref:actsUpon ?room .
?room rdf:type saref4bldg:BuildingSpace .
?preference saref4bldg:hasIndoorTemperatureSetpoint ?temp .

# Energy price (Energy Market)
?price rdf:type saref4ener:Price .
?price saref:hasValue ?eurPerKwh .
?price saref4ener:relatesToCommodity saref4ener:Electricity .

# Grid balance (Grid Operator)
?balance rdf:type saref:Measurement .
?balance saref:relatesToProperty saref4ener:Power .
?balance saref4ener:relatesToDirection ?direction .
?balance saref:hasValue ?watts .
```

---

## Variations

### Lite version (15 min, no hardware)

If hardware is not available, the same game works with **laptops only**:

- Each table runs a Python KB script with a terminal UI
- Instead of physical sensors, participants type values or click buttons
- Instead of LED tubes, the central projector shows animated connections
- Still powerful: the semantic matching + reasoning demo stands on its own

### Workshop version (half-day)

Participants **build their own KB** during the workshop:

1. (30 min) Intro to KE concepts
2. (60 min) Each team writes a Python KB with 2 interactions
3. (30 min) Teams register with the shared KE runtime and discover each other
4. (30 min) Play the District 2030 game with their own code
5. (30 min) Debrief: what they learned about semantic interoperability

### Exhibition version (continuous, unmanned)

A self-running demo with pre-programmed scenarios cycling every 5 minutes.
Visitors can press buttons to inject events. A large display explains what
is happening. Works well at trade shows or EU project review meetings.

---

## Why this works

The fundamental problem with demoing the Knowledge Engine today is that its
value is *architectural* -- it's about what happens in the network, not in any
single component. You can't show semantic matching with a screenshot.

This game makes the invisible visible:

- **The network IS the room.** You're standing inside the architecture diagram.
- **Knowledge flow IS light.** When the reasoner chains three KBs, you
  physically see a pulse travel from table to table to table.
- **Failure IS darkness.** When a node drops, its corner of the room goes dark.
  When it recovers, light returns.
- **Joining IS awakening.** A new node plugging in and its lantern coming
  alive is viscerally satisfying.
- **Semantic matching IS colour.** You see that orange (energy) data and
  blue (comfort) data flow to different destinations without anyone configuring
  routes.

The best conference demos are the ones people talk about at dinner. "They
turned the room into a power grid and I could see the data flowing through
tubes of light" is that kind of story.
