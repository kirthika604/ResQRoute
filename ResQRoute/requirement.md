# Smart Ambulance Emergency Road Alert System

## 1. Project Overview

The Smart Ambulance Emergency Road Alert System is a real-time hardware
and software platform designed to demonstrate how an ambulance can
automatically communicate its emergency status, current location, and
planned route to an intelligent backend, which then warns upcoming
roadside nodes before the ambulance reaches them.

The system is intended as a **serious hackathon/project-expo prototype**
using real hardware, real GPS data, real-time communication, route
computation, predictive road alerts, and a live monitoring dashboard.

The prototype will operate in a **controlled miniature/test
environment**. It is not intended for direct deployment on public roads
or integration with government traffic infrastructure.

------------------------------------------------------------------------

## 2. Primary Objective

Build a working end-to-end system in which:

1.  An ambulance prototype enters emergency mode.
2.  The ambulance hardware obtains its real-time location.
3.  The location is transmitted to the backend.
4.  The backend determines or receives the ambulance destination.
5.  A routing engine calculates the ambulance route.
6.  The backend identifies upcoming road segments/nodes on that route.
7.  The system calculates when each relevant roadside node should be
    alerted.
8.  The appropriate roadside node activates a warning.
9.  The warning remains active while the ambulance is approaching.
10. The warning is cleared after the ambulance passes.
11. The system continuously updates the route and alerts if the
    ambulance changes direction or route.
12. A dashboard displays the complete process in real time.

------------------------------------------------------------------------

# 3. Core System Architecture

``` text
                    ┌──────────────────────┐
                    │   Ambulance Unit     │
                    │                      │
                    │ ESP32                │
                    │ GPS/GNSS             │
                    │ Emergency Switch     │
                    │ IMU                  │
                    │ OLED                 │
                    └──────────┬───────────┘
                               │
                         Live location
                         + emergency state
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Communication Layer  │
                    │                      │
                    │ Wi-Fi initially      │
                    │ 4G/LTE later         │
                    └──────────┬───────────┘
                               │
                               ▼
              ┌────────────────────────────────┐
              │          Backend Server        │
              │                                │
              │ FastAPI                         │
              │ PostgreSQL/PostGIS              │
              │ Routing Engine                  │
              │ Route Processing                │
              │ Road Matching                   │
              │ Alert Decision Engine            │
              │ WebSocket/MQTT communication     │
              └───────────────┬────────────────┘
                              │
                 Alert commands│
                              │
             ┌────────────────┴─────────────────┐
             │                                  │
             ▼                                  ▼
   ┌───────────────────┐              ┌───────────────────┐
   │ Roadside Node 1   │              │ Roadside Node 2   │
   │ ESP32             │              │ ESP32             │
   │ LED Warning       │              │ LED Warning       │
   │ OLED/Buzzer       │              │ OLED/Buzzer       │
   └───────────────────┘              └───────────────────┘

                              │
                              ▼
                    ┌──────────────────────┐
                    │   Live Dashboard     │
                    │                      │
                    │ Ambulance position   │
                    │ Route                 │
                    │ Upcoming road         │
                    │ ETA                   │
                    │ Alert status          │
                    │ Node status           │
                    │ Emergency status      │
                    │ Event log             │
                    └──────────────────────┘
```

------------------------------------------------------------------------

# 4. Hardware Requirements

## 4.1 Basic Electronics Kit

The initial electronics kit should contain:

-   ESP32 development board × 1
-   830-point breadboard × 1
-   Micro-USB cable × 1
-   Male-to-male Dupont wires
-   Male-to-female Dupont wires
-   Female-to-female Dupont wires
-   Push-button switches
-   Resistors
-   LEDs
-   RGB LEDs
-   Active buzzer
-   Passive buzzer
-   Basic supporting components

The generic electronics kit does not need to contain the
project-specific modules.

------------------------------------------------------------------------

## 4.2 Ambulance Unit

Required:

-   ESP32 development board × 1
-   GPS/GNSS module × 1
-   Emergency activation push button/switch × 1
-   OLED display × 1
-   MPU6050 IMU × 1
-   USB power source/power bank during development
-   Jumper wires
-   Breadboard or compact prototype board

Optional later:

-   4G/LTE communication module
-   Better GNSS receiver
-   Battery management circuit
-   Enclosure
-   Vehicle speed sensor
-   Additional status LEDs

------------------------------------------------------------------------

## 4.3 Roadside Alert Node

Initial prototype:

-   ESP32 development board × 1 per node
-   WS2812B/addressable RGB LEDs
-   Standard LEDs
-   Buzzer
-   OLED display if required
-   Breadboard/prototype board
-   Jumper wires
-   USB power source

The final demonstration should support multiple roadside nodes.

Recommended demonstration:

-   Node 1: upcoming road
-   Node 2: following road
-   Node 3: alternate/intersection road
-   Node 4: optional additional node

The number of physical nodes can be increased after the core system
works.

------------------------------------------------------------------------

## 4.4 Communication Hardware

### Initial development

Use Wi-Fi where practical to validate the complete system.

### Final prototype

Evaluate a cellular communication option such as:

-   ESP32 + 4G/LTE modem
-   SIM7600/SIM7670-class module or equivalent

The final cellular module must be selected after testing:

-   Indian network compatibility
-   Power requirements
-   ESP32 interface compatibility
-   Data reliability
-   Antenna requirements
-   Availability of suitable SIM/data service

Do not lock the project to a specific cellular module before hardware
testing.

------------------------------------------------------------------------

# 5. Software Requirements

## 5.1 Embedded Software

The ESP32 firmware must support:

-   Hardware initialization
-   GPS data acquisition
-   Emergency switch detection
-   IMU data acquisition
-   OLED status display
-   LED control
-   Buzzer control
-   Network connection
-   Communication with backend
-   Heartbeat/status messages
-   Command reception
-   Alert activation/deactivation
-   Error handling
-   Connection-loss handling
-   GPS-loss handling

------------------------------------------------------------------------

# 6. Backend Requirements

Recommended backend:

-   Python
-   FastAPI
-   PostgreSQL
-   PostGIS

The backend shall provide:

### 6.1 Ambulance Management

-   Ambulance registration
-   Ambulance ID
-   Emergency status
-   Current coordinates
-   Current heading
-   Current speed if available
-   Last communication timestamp
-   Connection status

### 6.2 Route Management

The backend must:

-   Accept destination information
-   Calculate a route
-   Store the active route
-   Divide/represent the route as road segments
-   Match incoming GPS positions to the route
-   Identify upcoming road segments
-   Calculate estimated arrival times
-   Recalculate when necessary

------------------------------------------------------------------------

# 7. Routing Requirements

The system must use an actual routing engine rather than a manually
hardcoded sequence of roads.

Possible routing engines:

-   OSRM
-   GraphHopper
-   OpenRouteService
-   Another suitable routing engine

The routing system should support:

-   Start location
-   Destination
-   Road network
-   Route geometry
-   Distance
-   Estimated travel time
-   Road/segment identification where available

------------------------------------------------------------------------

# 8. Route-Aware Alert Engine

This is a core feature of the project.

The system must not simply broadcast an emergency message to every
roadside node.

Instead, the backend must determine:

1.  Where the ambulance currently is.
2.  Which route it is following.
3.  Which roadside nodes are on or near that route.
4.  Which node comes next.
5.  How far the ambulance is from that node.
6.  Estimated time until arrival.
7.  Whether that node should currently be alerted.

------------------------------------------------------------------------

## 8.1 Alert Activation Logic

A roadside node should activate when:

``` text
Ambulance is in emergency mode
        AND
Node is relevant to the active route
        AND
Ambulance is approaching the node
        AND
ETA is within configured alert threshold
        AND
Communication is valid
```

The threshold should be configurable.

Example:

``` text
> 90 seconds away  → No alert
60–90 seconds      → Prepare/pre-alert
30–60 seconds      → Active warning
< 30 seconds       → High-priority warning
After passing      → Clear
```

These values are prototype parameters and must be configurable rather
than permanently hardcoded.

------------------------------------------------------------------------

# 9. Dynamic Route Change

The system must support route changes.

Example:

``` text
Original route:

Ambulance
    ↓
Road A
    ↓
Road B
    ↓
Road C
```

If the ambulance changes route:

``` text
Ambulance
    ↓
Road A
    ↓
Road D
    ↓
Road E
```

The backend must:

-   Detect the route deviation
-   Recalculate the route
-   Remove alerts associated with the old route where appropriate
-   Identify new upcoming nodes
-   Activate the correct nodes
-   Update the dashboard

This feature is important for demonstrating that the system is genuinely
route-aware.

------------------------------------------------------------------------

# 10. Roadside Node Requirements

Each roadside node should have a unique ID.

Example:

``` text
NODE-001
NODE-002
NODE-003
NODE-004
```

Each node should have:

-   Node ID
-   Geographic/testbed position
-   Network status
-   Current alert state
-   Last heartbeat
-   Optional display state

Possible states:

``` text
OFFLINE
ONLINE
READY
PRE_ALERT
ALERT_ACTIVE
AMBULANCE_PASSED
FAULT
```

------------------------------------------------------------------------

# 11. Alert Output

The roadside node should provide a clearly visible physical warning.

Possible outputs:

### Visual

-   Red/blue RGB LED pattern
-   Flashing LED
-   OLED message
-   Direction/priority indicator

### Audio

-   Buzzer

The prototype should prioritize visual feedback because it is easier for
an exhibition audience to understand.

Example OLED:

``` text
EMERGENCY ALERT

AMBULANCE
APPROACHING

ETA: 28 sec
```

------------------------------------------------------------------------

# 12. Ambulance OLED Requirements

The ambulance OLED can display:

``` text
AMBULANCE UNIT

STATUS: EMERGENCY
GPS: LOCKED

LAT: XX.XXXX
LON: XX.XXXX

NETWORK: ONLINE
```

During normal operation:

``` text
STATUS: READY
GPS: LOCKED
NETWORK: ONLINE
```

During failure:

``` text
GPS: LOST
NETWORK: ONLINE
```

or

``` text
NETWORK: LOST
```

------------------------------------------------------------------------

# 13. Dashboard Requirements

The dashboard is a major part of the demonstration.

It should display:

## Live Map

-   Ambulance location
-   Ambulance marker
-   Active route
-   Route direction
-   Roadside nodes
-   Node states
-   Alert radius/zone if useful

## Ambulance Information

-   Ambulance ID
-   Emergency state
-   GPS status
-   Network status
-   Current coordinates
-   Current speed if available
-   Current heading

## Route Information

-   Destination
-   Route distance
-   Remaining distance
-   ETA
-   Next road/node
-   Upcoming road sequence

## Roadside Status

Example:

``` text
NODE-001    ACTIVE
NODE-002    READY
NODE-003    ACTIVE
NODE-004    OFFLINE
```

## Event Log

Example:

``` text
20:31:04  Emergency activated
20:31:06  GPS position received
20:31:07  Route calculated
20:31:12  NODE-002 pre-alert activated
20:31:31  NODE-002 alert active
20:32:02  Ambulance passed NODE-002
20:32:03  NODE-002 alert cleared
```

------------------------------------------------------------------------

# 14. Real-Time Communication

The system should support real-time updates.

Possible technologies:

-   WebSockets
-   MQTT
-   HTTP REST + polling for early development

Recommended architecture:

``` text
REST API
    +
WebSocket/MQTT
```

REST can handle:

-   Registration
-   Route creation
-   Configuration
-   Historical data

WebSockets/MQTT can handle:

-   Live GPS updates
-   Emergency state
-   Node status
-   Alert commands
-   Dashboard updates

------------------------------------------------------------------------

# 15. Database Requirements

PostgreSQL/PostGIS is recommended.

Possible tables:

``` text
ambulances
road_nodes
routes
route_segments
gps_positions
alerts
events
device_status
```

### Ambulances

``` text
id
name
device_id
status
latitude
longitude
heading
speed
last_seen
```

### Road Nodes

``` text
id
node_id
latitude
longitude
status
last_seen
```

### Alerts

``` text
id
ambulance_id
node_id
route_id
alert_type
activated_at
cleared_at
eta
status
```

### GPS Positions

``` text
id
ambulance_id
latitude
longitude
speed
heading
timestamp
```

------------------------------------------------------------------------

# 16. Failure Handling

The system must not blindly assume that everything is working.

It must detect:

### GPS Failure

If GPS data stops:

``` text
GPS LOST
```

The dashboard should show the ambulance location as stale rather than
pretending it is live.

### Network Failure

If the ambulance loses communication:

``` text
AMBULANCE OFFLINE
```

The backend should record the last known position.

### Roadside Node Failure

If a node stops sending heartbeats:

``` text
NODE-003 OFFLINE
```

The dashboard should indicate that the alert cannot be reliably
delivered to that node.

### Route Failure

If no valid route is available:

``` text
ROUTE UNAVAILABLE
```

The system must not activate random roadside alerts.

------------------------------------------------------------------------

# 17. Security Requirements

The prototype should include basic device authentication.

Each device should have:

-   Unique device ID
-   Authentication token/API key
-   Server-side validation

The backend must reject unknown devices.

Communication should use secure protocols where practical.

For the final prototype:

-   HTTPS
-   Secure WebSocket
-   Authenticated MQTT if used

------------------------------------------------------------------------

# 18. Logging Requirements

The system should maintain an event history.

Log events such as:

-   Emergency activation
-   Emergency deactivation
-   GPS lock/loss
-   Network connection/loss
-   Route creation
-   Route recalculation
-   Alert activation
-   Alert cancellation
-   Ambulance passing node
-   Node going offline
-   Node reconnecting

This information should be visible in the dashboard.

------------------------------------------------------------------------

# 19. Physical Demonstration Requirements

The final project should be demonstrated using a controlled miniature
environment.

The physical model may contain:

-   Miniature roads
-   Intersections
-   Roadside alert nodes
-   Model ambulance
-   LEDs
-   Direction signs
-   Optional miniature vehicles
-   Laptop/dashboard

The ambulance should be physically moved through the test environment.

The system should react to the ambulance's actual GPS/test coordinates
or a controlled location simulation where GPS accuracy is insufficient
indoors.

------------------------------------------------------------------------

# 20. Demonstration Scenario

## Scenario A --- Normal Emergency

``` text
1. Ambulance is idle.
2. Emergency button is pressed.
3. Ambulance enters EMERGENCY state.
4. GPS position is obtained.
5. Destination is selected.
6. Route is calculated.
7. Upcoming nodes are identified.
8. First relevant node enters PRE_ALERT.
9. Node becomes ACTIVE as ambulance approaches.
10. Ambulance passes the node.
11. Alert is cleared.
12. Next node becomes active.
```

------------------------------------------------------------------------

## Scenario B --- Route Change

``` text
1. Ambulance enters emergency mode.
2. Route A is calculated.
3. Nodes on Route A receive alerts.
4. Ambulance changes direction.
5. Backend detects route deviation.
6. Route B is calculated.
7. Old unnecessary alerts are cancelled.
8. New route nodes are identified.
9. New alerts are activated.
```

------------------------------------------------------------------------

## Scenario C --- Communication Failure

``` text
1. Ambulance is transmitting normally.
2. Network connection is interrupted.
3. Backend detects missing heartbeat.
4. Dashboard marks ambulance OFFLINE.
5. Last known position is displayed with stale status.
6. System avoids presenting outdated information as live.
7. Connection is restored.
8. Ambulance reconnects.
9. Current state is synchronized.
```

------------------------------------------------------------------------

# 21. Development Phases

## Phase 1 --- Hardware Basics

-   ESP32 setup
-   Breadboard setup
-   LEDs
-   Buttons
-   Buzzer
-   OLED
-   Basic GPIO testing

## Phase 2 --- Ambulance Unit

-   GPS integration
-   Emergency switch
-   OLED
-   IMU
-   ESP32 firmware
-   Local status handling

## Phase 3 --- Backend

-   FastAPI
-   PostgreSQL/PostGIS
-   Device registration
-   GPS API
-   Emergency state API
-   WebSocket/MQTT

## Phase 4 --- Routing

-   Routing engine
-   Route generation
-   GPS map matching
-   Upcoming road/node detection
-   ETA calculation

## Phase 5 --- Roadside Node

-   Second ESP32
-   LED warning
-   Buzzer
-   Node registration
-   Remote alert commands
-   Heartbeat

## Phase 6 --- Alert Engine

-   Route-aware alerting
-   ETA thresholds
-   Alert activation
-   Alert cancellation
-   Node sequencing
-   Route change handling

## Phase 7 --- Dashboard

-   Live map
-   Route
-   Ambulance status
-   Node status
-   ETA
-   Alerts
-   Event log

## Phase 8 --- Integration

Integrate:

``` text
Hardware
    +
Communication
    +
Backend
    +
Routing
    +
Alert Engine
    +
Roadside Nodes
    +
Dashboard
```

## Phase 9 --- Reliability Testing

Test:

-   GPS loss
-   Network loss
-   Node failure
-   Route change
-   Rapid movement
-   Incorrect GPS data
-   Duplicate messages
-   Backend restart
-   Device reconnect
-   Multiple nodes

## Phase 10 --- Exhibition Model

Build the final miniature road environment and perform a complete live
demonstration.

------------------------------------------------------------------------

# 22. Recommended Technology Stack

## Hardware

-   ESP32
-   GNSS/GPS
-   MPU6050
-   OLED
-   WS2812B
-   LEDs
-   Buzzer
-   Push buttons
-   4G/LTE module later

## Embedded

-   Arduino framework or ESP-IDF
-   C/C++

## Backend

-   Python
-   FastAPI
-   PostgreSQL
-   PostGIS

## Routing

-   OSRM / GraphHopper / OpenRouteService

## Real-Time Communication

-   WebSocket
-   MQTT

## Frontend

Possible options:

-   React
-   Next.js
-   Leaflet

## Deployment

During development:

``` text
Local computer
    ├── Backend
    ├── Database
    ├── Routing service
    └── Dashboard
```

Later, selected components can be deployed to a cloud server.

------------------------------------------------------------------------

# 23. Functional Requirements

The completed prototype must:

-   [ ] Detect emergency activation
-   [ ] Acquire ambulance location
-   [ ] Transmit live location
-   [ ] Display ambulance state
-   [ ] Register roadside nodes
-   [ ] Calculate an actual route
-   [ ] Identify upcoming road nodes
-   [ ] Calculate ETA
-   [ ] Activate relevant warnings
-   [ ] Clear warnings after passage
-   [ ] Recalculate route after route changes
-   [ ] Cancel obsolete alerts
-   [ ] Display live system status
-   [ ] Record system events
-   [ ] Detect device failures
-   [ ] Handle GPS failure
-   [ ] Handle communication failure
-   [ ] Handle roadside-node failure

------------------------------------------------------------------------

# 24. Non-Functional Requirements

## Reliability

The system should continue operating correctly despite temporary
communication or sensor failures.

## Low Latency

Location and alert updates should be delivered with low enough latency
for meaningful advance warning in the prototype.

## Scalability

The backend architecture should support multiple ambulances and multiple
roadside nodes even if the physical demonstration uses only one
ambulance.

## Maintainability

Hardware, routing, alert logic, backend, and frontend should be
separated into modular components.

## Observability

Important system events must be logged and visible.

## Safety

The prototype must not directly control real public traffic signals or
interfere with real traffic infrastructure.

------------------------------------------------------------------------

# 25. Project Constraints

-   Initial deployment is a controlled demonstration.
-   No direct public-road deployment.
-   No direct traffic-light control.
-   No assumption that every vehicle has an internet-connected device.
-   GPS accuracy may be reduced indoors.
-   Cellular communication may require additional hardware and SIM/data
    service.
-   Alert thresholds must be configurable.
-   Hardware quantities may increase as the prototype evolves.

------------------------------------------------------------------------

# 26. Initial Hardware Purchase

### Basic Kit

The first purchase should contain:

-   ESP32
-   Breadboard
-   Jumper wires
-   Push buttons
-   Resistors
-   LEDs
-   Buzzer
-   USB cable
-   Basic prototyping components

### Project-Specific Components

Purchase separately:

-   GPS/GNSS module
-   MPU6050
-   Second ESP32
-   WS2812B/addressable LEDs
-   Additional OLED if required
-   4G/LTE module later
-   Additional roadside-node hardware later

------------------------------------------------------------------------

# 27. Success Criteria

The project will be considered successfully demonstrated when the
following complete sequence works live:

``` text
Emergency button pressed
        ↓
Ambulance enters emergency mode
        ↓
GPS location obtained
        ↓
Location transmitted
        ↓
Backend receives location
        ↓
Route calculated
        ↓
Upcoming roadside node identified
        ↓
ETA calculated
        ↓
Node receives alert
        ↓
Physical warning activates
        ↓
Ambulance approaches
        ↓
Warning remains active
        ↓
Ambulance passes
        ↓
Warning clears
        ↓
Next relevant node is activated
```

A stronger demonstration should additionally show:

``` text
Ambulance changes route
        ↓
Old route alerts cancelled
        ↓
New route calculated
        ↓
New roadside nodes identified
        ↓
New alerts activated
```

------------------------------------------------------------------------

# 28. Final Project Goal

The final prototype should demonstrate a **real-time, route-aware
emergency road alert network**, rather than simply an ambulance GPS
tracker.

The key technical chain is:

``` text
REAL HARDWARE
      ↓
REAL LOCATION
      ↓
REAL-TIME COMMUNICATION
      ↓
ROUTE COMPUTATION
      ↓
PREDICTIVE ROAD IDENTIFICATION
      ↓
INTELLIGENT ALERT DECISION
      ↓
PHYSICAL ROADSIDE WARNING
      ↓
LIVE DASHBOARD
```

The project should be designed so that the hardware demonstration,
backend intelligence, routing system, alert engine, and dashboard form
one integrated system.
