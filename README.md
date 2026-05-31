# E_ATV--E_Baja
Electric powertrain design for BAJA SAE  - covers motor selection, battery system, transmission, circuit design, and validation.

Table of Contents

Overview
Performance Targets (SAE Rule Book)
Powertrain Architecture
Electric Motor
Battery Pack & BMS
Circuit Diagram
Transmission System
Driveline & Wheel Assembly
Force Calculation & Design Validation
DC-DC Buck Converter
DVP
DFMEA
Lessons Learnt
---

**Overview**

The powertrain is the complete system that transfers energy from the battery to the wheels. In this electric vehicle, the energy flows through:

**Battery → BMS → Motor Controller → Motor → Gearbox → Drive Shaft → CV Joints → Wheel**

The goal is to deliver torque (rotational force) and power efficiently to the tires so the vehicle moves correctly across rough terrain.



**Performance Targets (SAE Rule Book)**

These are the minimum targets set by the BAJA SAE Rulebook that every competing team must meet:

| Parameter       | Minimum Requirement     |
|-----------------|-------------------------|
| Top Speed       | > 40 km/h               |
| Gradeability    | > 35°                   |
| Drivetrain Efficiency | > 85%             |
| Starting Torque | High (adequate for hill start) |

**Gradeability** is the steepest slope the vehicle can climb without stalling or losing traction.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------

## Powertrain Architecture

<img width="758" height="439" alt="Picture1" src="https://github.com/user-attachments/assets/6f9906f9-0431-4317-9dd9-aa2fb1f2587b" />

The powertrain follows a **rear-wheel drive** layout with a direct-coupled, single fixed-ratio automatic transmission. Since electric motors produce usable torque from 0 RPM, no clutch or manual gear selection is needed — the drivetrain is inherently "**automatic**."

Key subsystems:
- **HV (High Voltage) Path** — Battery → Contactor → Fuse → Motor Controller → Motor
- **LV (Low Voltage) Path** — Auxiliary battery powering VCU, sensors, relays, BMS
- **Control** — VCU (Arduino-based) manages ignition, start/stop, throttle, brake, kill switch, and NDR signals
- **Safety** — TSAL, Brake Light, AIR (Accumulator Isolation Relay), Kill Switch, FU1, FU2, BMS

------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Electric Motor**

**Type: PMSM — Permanent Magnet Synchronous Motor**

A PMSM uses permanent magnets embedded in the rotor. The stator creates a rotating magnetic field and the rotor's magnets synchronize with it. This makes PMSM motors ideal for EV applications due to:

- Very high efficiency (typically 92–97%)
- High power density (more power per kg)
- Excellent torque at all speeds, including zero RPM
- Very smooth torque delivery
- Regenerative braking capability

The motor is controlled using **FOC (Field Oriented Control)**, which provides precise torque control by independently managing the d-axis (flux) and q-axis (torque) current components via a three-phase inverter.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Battery Pack & BMS-Battery Management Systems**

**Chemistry: Li-ion NMC (Lithium Nickel Manganese Cobalt Oxide)**

NMC is the most widely used EV battery chemistry because of its high energy density, good power density, reasonable cycle life, and better thermal stability compared to NCA chemistry.

The **BMS (Battery Management System)** uses **active balancing** to keep all cells at equal voltage, maximising usable capacity and lifespan. It continuously monitors voltage, current, and temperature, and limits discharge current to protect the pack.

The **AIR (Accumulator Isolation Relay)** isolates the battery pack from the rest of the HV system in any emergency. All HV switching is handled by automotive-grade contactors and AIRs — the VCU only controls low-voltage relay coils and CAN commands.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Circuit Diagram

<img width="1282" height="658" alt="image" src="https://github.com/user-attachments/assets/36cee0e1-741c-4e30-afe8-e34c0d9e763a" />

The circuit shows the full HV and LV architecture:
- **HV side (left):** HV Battery → AIR → Contactor → FU1 (main fuse) → Motor Controller → PMSM Motor and TSAL
- **LV side (right):** Auxiliary Battery → FU2 → all low-voltage loads (sensors, relays, BMS, brake light, horn)
- **VCU (Arduino):** Receives inputs from Ignition, Start/Stop, Brake, Throttle, Kill Switch, NDR; outputs to AIR coil, TSAL relay, BL relay, RL2

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Transmission System

**Type: Automatic — Single Fixed-Ratio, Two-Stage Spur Gearbox**

A spur gear was selected for its simplicity, high efficiency per stage (~98–99%), and ease of manufacture. Two reduction stages are used to achieve a high overall gear ratio while keeping individual gear sizes manageable.

- The gearbox casing is made from **Al 6082 T6** — a lightweight aluminium alloy with good machinability and structural strength.
- **Direct coupling** between motor output shaft and gearbox input eliminates chains and belts, minimising power loss and backlash.
- Overall drivetrain efficiency target: **≥ 85%** (matching SAE requirement).

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Driveline & Wheel Assembly**

**Driveline: Rear Wheel Drive (RWD)**

<img width="573" height="325" alt="image" src="https://github.com/user-attachments/assets/cbf9d45a-2f28-4189-944c-a8f04d24b61f" />

The power flow is: **PMSM Motor → Motor Shaft → Coupling → 2-Stage Reduction Gearbox → Drive Shaft → Wheel → Tire**

**Drive Shaft**
- **Type:** Solid Shaft
- **Material:** EN8 — a medium carbon steel (0.36–0.44% carbon) offering good tensile strength (~700 MPa UTS), toughness, and machinability, suitable for transmission shafts under moderate-to-high loads.
- **Joints:** CV (Constant Velocity) Joints — allow the drive shaft to transmit torque at variable angles without changing rotational velocity, essential for suspension travel.

**Front Wheel Assembly**
The front wheel uses a **double wishbone suspension** configuration with upper and lower A-arms (wishbones), a damper (shock absorber), knuckle, rotor, and hub. The damper resists compression and extension, absorbing impact energy from terrain.

<img width="543" height="316" alt="image" src="https://github.com/user-attachments/assets/17d30117-e173-4b80-9d2b-2907b9363db9" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Force Calculation & Design Validation**

Design assumptions were made per SAE BAJA 2025 regulations:
- Max vehicle velocity: **45 km/h**
- Max gradeability: **35°**
- Drivetrain efficiency: **85%**

These drive 
- The selection of the motor
- Gear ratio
- Shaft dimensions.
  
  Resistive forces considered include rolling resistance, aerodynamic drag, and grade resistance. The net driving force is calculated from motor torque, gear ratio, and tyre radius.

The **PMSM Motor Controller** uses **Field Oriented Control (FOC)**  - a Simulink model validates the control loop with RPM reference, iabc sensors, wSens (angular velocity), thSens (rotor angle from encoder), and Vdc monitoring.

<img width="1029" height="523" alt="image" src="https://github.com/user-attachments/assets/b6f2a21f-9664-4635-b916-15998cd5bf6c" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**DC-DC Buck Converter**

A **step-down DC-DC switching power supply** powers the 12V auxiliary systems from the main HV battery. Unlike a linear regulator, a buck converter switches rapidly and uses inductors and capacitors to efficiently transfer energy, achieving ~92% efficiency.

**Controlled via a PI(s) feedback loop** that compares output voltage to a 12V reference and adjusts the MOSFET duty cycle to maintain stable output regardless of load changes.

<img width="1424" height="742" alt="image" src="https://github.com/user-attachments/assets/04e1c71a-7745-48b4-9933-97e41ae63f58" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Power Train Design Flow**

<img width="1863" height="994" alt="image" src="https://github.com/user-attachments/assets/a0671acc-ba09-484c-aaa4-5279cd61f72f" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## DVP

| Test              | Acceptance Criteria     | Method                  | Tool          |
|-------------------|-------------------------|-------------------------|---------------|
| Max Speed Test    | > 40 km/h               | Straight path run       | IPG CarMaker  |
| Acceleration Test | > 5 m/s²                | Straight path run       | IPG CarMaker  |
| Gradeability Test | > 35°                   | 35° uphill slope        | IPG CarMaker  |

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**DFMEA**

DFMEA (Design Failure Mode and Effects Analysis) is a systematic engineering tool to identify potential failures before they happen, assess their severity, and design countermeasures.

**RPN (Risk Priority Number) = Severity × Occurrence × Detection**

| Item             | Failure Mode               | Effect                        | Recommended Action                              |
|------------------|----------------------------|-------------------------------|--------------------------------------------------|
| Motor Controller | Driver IC / control module failure | Motor control lost      | Monitor controller; keep away from dirt/pollution |
| BMS              | Voltage detection / wire failure / thermal runaway | Battery not monitored | Monitor supply; check hardware regularly |
| Wiring Harness   | Wire burning due to overheat | Vehicle immobility           | Use proper wire & fuse specifications            |
| Drive Shaft      | Shaft fracture              | Improper torque transmission  | Use proper material; precise machining           |
| Accelerator Pedal | Throttle signal interrupted | Reduced motor speed          | Use rated sensor; check wiring with insulation   |


