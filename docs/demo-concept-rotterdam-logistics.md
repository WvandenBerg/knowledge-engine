# The Container Game

## Conference Room Demo for the Knowledge Engine -- Logistics Edition

A serious game for 15-30 participants that turns a conference room into the
Port of Rotterdam hinterland corridor. Physical miniature containers move
between tables while LED-lit corridors show the knowledge flowing underneath.

---

## The problem this demo makes tangible

Logistics Service Providers (LSPs) around Rotterdam share a structural problem:
**no single party sees the full picture.** A trucking company knows it is
driving an empty container back from Venlo. A barge operator has spare capacity
on tomorrow's sailing. A shipper in Duisburg needs a container picked up. A
terminal knows a vessel is delayed by 6 hours.

Today, these facts sit in separate TMS, ERP, and Excel systems. Coordination
happens through phone calls, emails, and Portbase messages -- point-to-point,
brittle, and always too late.

The classic "solution" is a central platform: everyone uploads their data, a
central optimizer computes the best plan. But LSPs will never do this. Their
planning data is commercially sensitive. Sharing it with a platform means
sharing it with competitors.

**The Knowledge Engine solves this differently.** Each LSP keeps their own data.
They publish *what kinds of questions they can answer* and *what kinds of events
they emit* -- as semantic graph patterns. The KE matches supply and demand for
knowledge automatically, without anyone seeing data they shouldn't.

This demo makes that difference physically visible and viscerally
understandable in 40 minutes.

---

## Setting: "Mainport 2030"

The conference room represents the Rotterdam-hinterland corridor. A large
printed floor map (or projected map) shows:

```
                    NORTH SEA
                       |
              +-----------------+
              | DEEP SEA        |
              | TERMINAL        |  (Maasvlakte)
              | (Table 1)       |
              +-------+---------+
                      |
          +-----------+-----------+
          |                       |
  +-------+-------+     +--------+--------+
  | BARGE          |     | RAIL             |
  | OPERATOR       |     | OPERATOR         |
  | (Table 3)      |     | (Table 4)        |
  +-------+--------+     +--------+---------+
          |                        |
          +------+-------+---------+
                 |       |
        +--------+--+ +--+----------+
        | INLAND    | | SHIPPER /   |
        | TERMINAL  | | CONSIGNEE   |
        | (Table 5) | | (Table 6)   |
        +-----------+ +-------------+

  +----------------+     +------------------+
  | TRUCKING LSP   |     | PORT AUTHORITY / |
  | (Table 2)      |     | CUSTOMS          |
  | (roams freely) |     | (Table 7)        |
  +----------------+     +------------------+
```

Tables are physically arranged to roughly mirror this geography. The trucking
LSP table is deliberately placed on a wheeled cart so it can physically move
between locations -- because that's what trucks do.

---

## The tables

| Table | Role | Physical props | KE interactions |
|-------|------|---------------|-----------------|
| **Deep Sea Terminal** | Container arrival/departure | Stack of miniature containers (colour-coded), vessel arrival board (e-ink or flip display) | ANSWER: container location, ETA; POST: vessel arrival, container available |
| **Trucking LSP** | Road transport | Toy trucks, "fuel gauge" (LED bar), route cards | ANSWER: transport capacity, price; ASK: container pickup requests; POST: truck position, delivery confirmation |
| **Barge Operator** | Inland waterway transport | Toy barge, capacity slider (physical), sailing schedule board | ANSWER: barge capacity + schedule; REACT: booking requests; POST: departure, arrival |
| **Rail Operator** | Rail freight | Toy train, track switch lever (physical), timetable card | ANSWER: rail slot availability; REACT: booking requests; POST: departure, arrival |
| **Inland Terminal** | Container storage + distribution | Container yard grid (physical board with slots), demand cards | ANSWER: yard capacity, container positions; ASK: incoming container ETA; POST: pickup ready |
| **Shipper** | Cargo owner | Order cards (origin, destination, deadline, contents), priority dial | ASK: cheapest/fastest route for my container; POST: transport order; REACT: delivery confirmation |
| **Port Authority** | Oversight + customs | "Inspection" stamp, delay cards, corridor capacity gauge | ANSWER: clearance status, corridor capacity; POST: inspection hold, corridor restriction; REACT: all container movements |

---

## The physical elements

### Miniature containers

40-foot container models (~15 cm), 3D-printed or purchased (model railway
scale 1:87 / H0 gauge, widely available for ~EUR 3 each). Each container has:

- A **colour** indicating cargo type (blue = refrigerated, red = hazardous,
  green = general, yellow = empty)
- An **NFC tag** on the bottom with a URI (e.g., `http://example.org/container/MSCU-1234`)
- This URI is used in graph pattern bindings -- when you scan a container,
  the KB knows which semantic entity it represents

Each table has an **NFC reader pad** (EUR 15). When a container is physically
placed on the pad, the KB registers it: "this container is now at my location."
When it's removed, it departs.

### The corridors

Between the tables, physical **corridor tracks** made from flat aluminium
channels with embedded WS2812B LED strips, painted to look like:

- **Blue channel**: waterway (barge route, Maasvlakte -> Rhine corridor)
- **Grey channel**: road (truck routes, branching)
- **Dark grey channel**: rail (Betuweroute to hinterland)

When a container transport is planned via the KE, the corresponding corridor
lights up:

- **Steady blue glow**: barge booking confirmed
- **Pulsing orange**: truck en route
- **Steady white**: rail slot reserved
- **Red flash**: disruption on this corridor

When the KE reasoner chains multiple LSPs to plan a multi-modal route, you see
a **sequential cascade**: first the terminal segment lights, then the barge
segment, then the inland terminal segment -- the knowledge flowing hop by hop.

### Node markers

Each table has a **physical marker** -- a miniature version of what they
represent:

- Deep Sea Terminal: small crane model with LED in the boom
- Trucking LSP: truck with LED headlights
- Barge Operator: barge model with LED
- Rail Operator: signal post with red/green LED
- Inland Terminal: warehouse model with LED
- Shipper: factory model with LED
- Port Authority: harbour master tower with LED

These light up when the KB is registered and pulse during data exchange (same
principle as the lanterns in the District 2030 demo).

### Central screen

A projected dashboard showing:

- Map view with live container positions (updated via REACT subscriptions)
- KE network graph: which KBs are connected, which interactions are registered
- Scoreboard (see game mechanics below)
- Event ticker: "Barge RHINE-07 departed Maasvlakte with 12 containers"

---

## Game flow

### Act 1 -- "Port Awakens" (5 min)

The game master powers on tables one by one. Each KB registers with the
Knowledge Directory. As each node comes alive, its marker lights up and
the corridor LEDs nearest to it warm up.

The game master narrates: *"It's Monday morning at the Port of Rotterdam.
12,000 containers arrived over the weekend on three deep-sea vessels. The
hinterland is waiting."*

The Deep Sea Terminal places a stack of 20 miniature containers on their NFC
pad. The KB POSTs container-available events. The central screen populates.

**KE feature: dynamic discovery. No LSP was pre-configured to talk to
any other. They discover each other through semantic graph patterns.**

### Act 2 -- "Plan the Day" (10 min)

The Shipper draws **order cards** from a deck. Each card specifies:

- Container type needed (colour)
- Origin: Deep Sea Terminal
- Destination: Inland Terminal (or direct to Shipper)
- Deadline: "before 14:00" / "before end of day" / "no rush"
- Special requirements: "refrigerated chain", "customs pre-cleared", etc.

The Shipper places order cards on their table and triggers ASK interactions:
*"What is the cheapest route for a refrigerated container from Maasvlakte to
Duisburg, arriving before 14:00?"*

**Here's where the KE shines.** The Shipper's KB doesn't know who can answer
this. The graph pattern describes *what* they need:

```sparql
?route rdf:type :TransportRoute .
?route :hasOrigin <maasvlakte> .
?route :hasDestination <duisburg> .
?route :hasDeadline ?deadline .
?route :hasCost ?cost .
?route :handlesCargoType :Refrigerated .
```

No single LSP can answer this fully. But the **reasoner** chains:

1. Deep Sea Terminal (container is here, cleared at 09:00)
2. Barge Operator (sailing at 10:00, arrives inland terminal 16:00 -- too late!)
3. Trucking LSP (can do Maasvlakte -> Duisburg direct, arrives 13:30, EUR 800)
4. Rail Operator (slot at 09:30, arrives 12:00, EUR 400, but no reefer wagon)

The KE returns the feasible options to the Shipper. The corridors light up
showing each evaluated route. The audience sees the purple "reasoner cascade"
testing each path.

**KE features: multi-hop reasoning, automatic orchestration, semantic matching
across different LSP schemas, data stays at each LSP's table.**

### Act 3 -- "Move the Metal" (10 min, interactive)

The Shipper selects routes. Now containers must physically move.

The Trucking LSP physically picks up a container from the Deep Sea Terminal's
stack, places it on their toy truck, and wheels their cart along the grey
corridor to the destination. As the truck passes, orange LEDs pulse alongside.
At each checkpoint (NFC pad at each table), the container is scanned and the
KB POSTs a position update. The central screen shows the container moving in
real-time.

Meanwhile, the Barge Operator loads multiple containers onto their barge model
(a tray that holds 6 miniature containers). They physically carry the tray
along the blue waterway corridor. Blue LEDs follow them.

The Rail Operator has a small track section on their table. Containers placed
on the "train" (a tray on a track) are POSTed as loaded. When the train
"departs," white LEDs cascade down the rail corridor.

**Every physical movement generates KE events. Every KE event drives physical
lights. The digital and physical are fully coupled.**

### Act 4 -- "Disruption!" (10 min)

The game master plays **disruption cards**:

**Card: "Vessel EVER GIVEN II is delayed 8 hours"**
- Deep Sea Terminal updates vessel ETA via POST
- All downstream plans are affected
- The Shipper's REACTs fire: "my container will be late"
- Corridors that had confirmed bookings start flashing amber
- Tables must re-plan using ASK interactions
- The audience sees the ripple effect: one event at the terminal causes
  knowledge pulses across the entire room

**Card: "A15 motorway closed -- accident at Ridderkerk"**
- Port Authority POSTs a corridor restriction
- Trucking LSP's routes through that corridor turn red
- The Shipper ASKs for alternatives
- The reasoner now routes around: barge + last-mile truck, or rail
- Purple cascade shows the reasoner trying alternatives in real-time

**Card: "Customs hold on container MSCU-7721"**
- Port Authority physically takes a container off a table and places it in
  their "inspection zone"
- The container disappears from the Trucking LSP's plan
- New ASK/ANSWER cycles fire to find a replacement container
- The audience sees how one blocked container triggers a cascade of
  re-planning across multiple independent LSPs

**Card: "Flash sale: barge capacity 50% off for next hour"**
- Barge Operator POSTs a price update
- The Shipper's REACT fires and re-evaluates pending orders
- Containers shift from truck (orange corridors dim) to barge (blue
  corridors brighten)
- Modal shift becomes physically visible as the room's dominant colour
  changes from orange to blue

### Act 5 -- "The Empty Container Challenge" (5 min, competitive)

The game master reveals: *"There are 8 empty containers in the hinterland.
There are 8 export orders at the terminal. Nobody knows where the empties are
except the LSP currently holding them."*

Yellow (empty) containers are scattered across tables. Each LSP only knows
about the empties physically on their own table.

The Shipper ASKs: *"Where is the nearest empty 40ft container?"*

Without the KE, this requires calling every LSP. With the KE, the semantic
pattern matches every KB that has registered an ANSWER for container positions.
The ASK fans out, answers come back, the corridors light up showing where
empties are. The cheapest empty-repositioning route is computed by the reasoner
chaining location data + transport capacity + pricing from multiple LSPs.

The physical result: yellow containers begin converging toward the Deep Sea
Terminal from multiple directions simultaneously, each via a different lit
corridor.

**KE feature: this is the demo's climax -- no central platform, no data
sharing agreement, no bilateral API integration. Just semantic patterns, and
suddenly a network-wide optimisation emerges from local knowledge.**

---

## Scoring and competition

To add stakes, the game tracks:

| Metric | Points | How it's measured |
|--------|--------|-------------------|
| Containers delivered on time | +10 per container | Deadline on order card vs. arrival scan |
| Late deliveries | -5 per container | Overdue at scan |
| Empty kilometres avoided | +5 per shared container | Yellow containers reused instead of repositioned |
| Modal shift (truck -> barge/rail) | +3 per container | Corridor type used |
| Disruption recovery time | +1-10 bonus | Time from disruption card to re-plan complete |

Scores are shown live on the central screen. Teams can choose to cooperate
(share more knowledge = higher collective score) or protect information
(register fewer ANSWER interactions = competitors can't see your capacity,
but network efficiency drops).

This creates a natural tension that mirrors the real logistics world:
**transparency vs. competitive advantage.** The KE enables a middle ground --
you share *what you can answer*, not *all your data*.

---

## What the audience takes away

| Message | How the game shows it |
|---------|-----------------------|
| **Data sovereignty**: LSPs keep control of their data | Each table has its own containers and planning -- nobody sees another table's full state |
| **No central platform needed** | There is no "master table" -- the KE runtime is infrastructure, not a data owner |
| **Semantic interoperability** | Trucking LSP and Barge Operator registered patterns independently, KE matched them |
| **Automatic orchestration** | The Shipper asks ONE question, the reasoner visits FOUR tables to assemble the answer |
| **Physical-digital twin** | Every container scan and physical movement is mirrored digitally, and vice versa |
| **Resilience to disruption** | Highway closure, vessel delay, customs hold -- the network re-routes autonomously |
| **Empty container reuse** | The "invisible" problem (where are the empties?) becomes trivially solvable |
| **Modal shift incentivisation** | Price changes by the barge operator physically shift container flow to waterways |

---

## Ontology: logistics graph patterns

We define a lightweight logistics ontology aligned with existing standards
(W3C SSN/SOSA for observations, GS1 for logistics events, UN/CEFACT for
trade). Alternatively, extend SAREF4TRAN (transportation) if available.

```sparql
# Prefixes
@prefix lgst: <http://knowledge-engine.eu/demo/logistics#> .
@prefix cont: <http://knowledge-engine.eu/demo/container#> .
@prefix geo:  <http://www.w3.org/2003/01/geo/wgs84_pos#> .
@prefix xsd:  <http://www.w3.org/2001/XMLSchema#> .
@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .

# Container location (ANSWER by any table holding containers)
?container rdf:type cont:Container .
?container cont:containerType ?type .
?container cont:currentLocation ?location .
?location geo:lat ?lat .
?location geo:long ?long .
?container cont:status ?status .

# Transport capacity offer (ANSWER by LSPs)
?offer rdf:type lgst:TransportOffer .
?offer lgst:fromLocation ?origin .
?offer lgst:toLocation ?destination .
?offer lgst:modality ?mode .
?offer lgst:departureTime ?departure .
?offer lgst:arrivalTime ?arrival .
?offer lgst:priceEur ?price .
?offer lgst:capacityTEU ?capacity .

# Transport order (POST by Shipper)
?order rdf:type lgst:TransportOrder .
?order lgst:container ?container .
?order lgst:origin ?origin .
?order lgst:destination ?destination .
?order lgst:deadline ?deadline .
?order lgst:cargoType ?cargoType .

# Container event (POST by any table when container moves)
?event rdf:type lgst:ContainerEvent .
?event lgst:container ?container .
?event lgst:eventType ?eventType .
?event lgst:location ?location .
?event lgst:timestamp ?timestamp .

# Corridor status (POST by Port Authority)
?corridor rdf:type lgst:Corridor .
?corridor lgst:corridorType ?mode .
?corridor lgst:fromLocation ?from .
?corridor lgst:toLocation ?to .
?corridor lgst:status ?status .
?corridor lgst:capacityPercent ?capacity .

# Route query (ASK by Shipper, answered by reasoner chaining LSPs)
?route rdf:type lgst:Route .
?route lgst:forContainer ?container .
?route lgst:origin ?origin .
?route lgst:destination ?destination .
?route lgst:arrivalTime ?eta .
?route lgst:totalCostEur ?cost .
?route lgst:legs ?legs .
```

### Reasoner domain knowledge (chaining rules)

```
# A route can be composed of multiple transport offers
IF
  ?offer1 lgst:toLocation ?midpoint .
  ?offer2 lgst:fromLocation ?midpoint .
  ?offer1 lgst:arrivalTime ?arr1 .
  ?offer2 lgst:departureTime ?dep2 .
  # arr1 <= dep2 (temporal feasibility)
THEN
  ?route lgst:hasLeg ?offer1 .
  ?route lgst:hasLeg ?offer2 .
  ?route lgst:origin ?origin1 .
  ?route lgst:destination ?destination2 .
```

This allows the reasoner to automatically compose multi-modal routes from
individual LSP capacity offers -- which is exactly the "magic moment" in the
demo.

---

## Bill of materials

### Per table (7 tables)

| Item | Approx. cost | Purpose |
|------|-------------|---------|
| Raspberry Pi 4 (or laptop) | EUR 50 | Runs KB Python script |
| NFC reader (RC522 or PN532) | EUR 15 | Scan containers on/off table |
| WS2812B LED strip (0.5m) | EUR 5 | Node marker illumination |
| Node marker model (3D-printed) | EUR 10 | Crane / truck / barge / train / warehouse / factory / tower |
| Push buttons (x3) | EUR 3 | Confirm bookings, trigger events |
| Small e-ink display (optional) | EUR 20 | Show local status, schedules |
| Jumper wires, breadboard | EUR 5 | Wiring |

Per-table cost: ~EUR 108 (with Pi) or ~EUR 58 (using laptops)

### Containers

| Item | Approx. cost | Purpose |
|------|-------------|---------|
| H0-scale 40ft containers x 30 | EUR 90 | Physical container tokens |
| NFC sticker tags x 30 | EUR 15 | Unique container identity |
| Container paint (4 colours) | EUR 10 | Cargo type coding |

### Corridor infrastructure

| Item | Approx. cost | Purpose |
|------|-------------|---------|
| WS2812B LED strips (15m total) | EUR 45 | Corridor illumination |
| Flat aluminium channels (15m) | EUR 30 | Corridor housing, painted per mode |
| Raspberry Pi (corridor controller) | EUR 50 | Drives all corridor LEDs |
| Floor map (printed vinyl, 4x3m) | EUR 80 | Rotterdam-hinterland geography |

### Shared infrastructure

| Item | Approx. cost | Purpose |
|------|-------------|---------|
| Laptop or Pi (KE runtime) | EUR 50 | Knowledge Directory + Smart Connectors |
| Wi-Fi router | EUR 30 | Local network |
| Projector + screen | -- | Central dashboard |
| Toy trucks x 3 (Bruder 1:16 or similar) | EUR 30 | Physical truck transport |
| Toy barge (custom tray, holds 6 containers) | EUR 15 | Physical barge transport |
| Wheeled cart for Trucking LSP | EUR 25 | Mobile table |

**Total for 7-table setup: ~EUR 1,100 - 1,400**

---

## Technical architecture

```
  [ Wi-Fi Router ]
        |
  +-----+------+------+------+------+------+------+
  |     |      |      |      |      |      |      |
 [T1]  [T2]  [T3]  [T4]  [T5]  [T6]  [T7]  [Corridor Pi]
  |     |      |      |      |      |      |      |
 NFC   NFC   NFC    NFC   NFC   NFC   NFC   LED strips
 LED   LED   LED    LED   LED   LED   LED   (15m total)
 Btns  Btns  Btns   Btns  Btns  Btns  Btns

  Each table Pi runs:
  - Python KB script (REST API client)
  - NFC reader driver (container scan)
  - Local LED driver (node marker)
  - Button handler (game actions)

  +---------- KE Runtime -----------+
  | (central laptop)                |
  |                                 |
  | - Knowledge Directory           |
  | - 8 Smart Connectors            |
  |   (7 tables + 1 corridor viz)   |
  | - Reasoner (level 4)            |
  | - Admin UI                      |
  | - Game master console           |
  +---------------------------------+

  +------- Corridor Visualiser -----+
  | (dedicated Pi)                  |
  | - REACT KB: subscribes to all   |
  |   ContainerEvent + CorridorStatus|
  | - Translates events to LED      |
  |   animations on corridor strips |
  +---------------------------------+
```

### Software components to build

1. **Table KB scripts** (Python, ~250 lines each, 7 variants)
   - Register KB with role-specific graph patterns
   - NFC scan handler: container arrives/departs
   - Button handlers: confirm booking, trigger local events
   - ASK/ANSWER/POST/REACT handlers via REST long-polling
   - Local LED driver for node marker

2. **Corridor Visualiser KB** (Python, ~400 lines)
   - REACT subscriptions for ContainerEvent and CorridorStatus patterns
   - Maps (origin, destination, mode) to physical LED strip segments
   - Animation engine: pulse, cascade, flash, steady glow
   - Colour mapping: blue=barge, orange=truck, white=rail, red=disruption,
     purple=reasoner

3. **Game master console** (web UI, ~500 lines)
   - Disruption card triggers (POST events to Port Authority KB)
   - Order card generator (POST to Shipper KB)
   - Score tracker
   - Pace control: pause/resume, skip to next act

4. **Central dashboard** (extends admin-ui or custom D3.js, ~400 lines)
   - Map view with container positions (updated via REACT)
   - Network graph from admin-ui
   - Scoreboard
   - Event log

5. **Domain knowledge rules** (loaded into reasoner)
   - Multi-modal route composition
   - Temporal feasibility constraints
   - Container type compatibility (reefer wagon, hazmat route)

---

## Variations

### Competitive variant: "LSP Rivals"

Instead of cooperative scoring, each LSP table competes for transport orders.
The Shipper always picks the cheapest/fastest option. LSPs can:

- Lower prices (but their "profit" score drops)
- Offer faster service (but they need to coordinate with others)
- Form alliances (register more ANSWER patterns with specific partners)
- Hoard information (register fewer ANSWER patterns -- but then the
  reasoner can't include you in multi-modal routes)

This variant powerfully demonstrates the game-theoretic incentives of
open vs. closed data sharing.

### EU project review variant: "FEDeRATED in Action"

Specifically tailored for EU logistics project reviews:

- Use actual project ontologies (DCSA, GS1 EPCIS, FEDeRATED semantic model)
- Tables represent real stakeholder types from the project
- Disruption scenarios map to real-world cases from project deliverables
- Scoring metrics align with project KPIs (modal shift %, CO2 reduction)

### Lite version (laptops only, 20 min)

Same game logic, no physical hardware:

- Containers are rows in a terminal UI / simple web page
- Corridors are animated lines on the central projector
- Participants click buttons instead of scanning NFC
- Still effective: the core demo is the *knowledge flow*, not the props

### Port Day / Open Day variant

For public-facing events at the Port of Rotterdam:

- Self-running with pre-scripted scenarios
- Large physical setup on a 3x5m table
- Visitors press buttons to trigger disruptions
- Display panels explain what's happening in plain language
- Containers move on motorised tracks (belt-driven) for extra visual impact

---

## Why this works (for the logistics audience)

The logistics industry has "platform fatigue." Every year, another startup
promises to be "the Uber of freight" or "the central platform for supply
chain visibility." LSPs are sceptical -- and rightly so. Sharing data with a
platform means losing control.

This demo makes a fundamentally different pitch *feel* different:

- **No table uploads anything to a central database.** The data literally
  stays on each table's Raspberry Pi. Audience members can verify this.
- **The KE runtime is infrastructure, not a data owner.** Like the internet
  routes packets without reading them, the KE routes knowledge without storing
  it.
- **You see the difference between bilateral and network effects.** When only
  2 tables are on, the corridors are mostly dark. When all 7 are on, the room
  comes alive with light. The value of the network is *visually proportional*
  to participation.
- **The empty container moment is the clincher.** Every logistics professional
  knows the empty container problem. Seeing it solved in real-time, across
  independent LSPs, without a central platform, without anyone revealing their
  full fleet position -- that's the "aha" moment.

The best thing a conference attendee can say afterwards is: *"They had actual
containers moving around the room and I could see the data flowing through the
floor. No central platform, and it still worked. I finally get it."*

---

## Relation to real-world initiatives

This demo concept aligns with several active programmes:

| Initiative | How the demo relates |
|-----------|---------------------|
| **FEDeRATED** | Demonstrates federated data sharing without central platform |
| **DTLF / eFTI** | Shows how electronic freight transport information can flow peer-to-peer |
| **iSHARE** | KE can integrate with iSHARE for trust/authorisation (future extension) |
| **Portbase** | Demo shows what "beyond Portbase" looks like: not replacing it, but extending the model to hinterland LSPs |
| **DCSA standards** | Container event patterns align with DCSA track & trace |
| **Horizon Europe clusters** (MAGPIE, PLANET, ALICE) | Demonstrates Physical Internet concepts at room scale |

This positioning matters for EU project proposals: the demo is both a
dissemination tool and a validation of the KE approach in the logistics domain.
