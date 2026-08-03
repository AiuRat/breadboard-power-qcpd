<p align="center">
  <a href="README.md">Русский</a> | <b>English</b> | <a href="README.de.md">Deutsch</a>
</p>

# Breadboard Power QC/PD

Combined power module for solderless breadboards supporting **USB Power Delivery (PD)** and **Quick Charge (QC)** fast charging protocols.

<p align="center">
  <img src="/production/images/assembled-pcb-top(pretty).jpg" width="850" alt="Breadboard Power QC/PD Assembled Board">
</p>

---

## 1. Project Description

**Breadboard Power QC/PD** is a compact, versatile device designed to solve the power supply problem for prototypes on solderless breadboards. Traditional laboratory power supplies are bulky and require manual adjustment, while cheap Chinese breadboard power modules are restricted to rigid $5\text{ V}$ and $3.3\text{ V}$ limits at low currents.

This project combines modern fast charging technologies via the **USB Type-C (QC/PD)** protocol, high-precision linear regulators, and the broad capabilities of switching DC-DC converters. The module plugs directly into the side rails of a breadboard and provides the engineer with independent power channels for digital logic, microcontrollers, and analog circuits (dual-polarity $\pm 15\text{ V}$ power supply).

---

## 2. Key Technical Specifications

* **Input Interface:** USB Type-C (supporting PD 3.0 / QC 3.0)
* **Connection Interface:** Dual-row pin headers with a $2.54\text{ mm}$ pitch (for standard breadboard rails)
* **Available Channels and Voltages:**
  * High-power adjustable channel (`VBUS`): **$5\text{ V}$ / $9\text{ V}$ / $12\text{ V}$ / $15\text{ V}$ / $20\text{ V}$** (determined by the trigger)
  * Logic power channel (`3.3Vout`): **$+3.3\text{ V}$** (low-noise linear LDO regulator)
  * Negative rail channel (`-15out`): **$-15\text{ V}$** (switching inverting converter)

---

## 3. Circuit Design & Device Architecture

Full electrical schematic diagram of the module:

<p align="center">
  <img src="/docs/images/breadboard-power-qcpd-schematic.png" width="850" alt="Schematic Diagram of Breadboard Power QC/PD">
</p>

### The module's architecture consists of three key sections:
1. **Fast charging trigger section:** Built on the specialized **CH224K** chip. It negotiates with the external power source (adapter) via `CC1/CC2` lines and "requests" the required voltage to the `VBUS` rail. The configuration is set by the `SA2` DIP switch.
2. **Linear LDO Regulator ($+3.3\text{ V}$):** Built on the **LD1086DT33TR** chip (TO-252 package) with input and output filter capacitors of $10\text{ }\mu\text{F}$.
3. **Switching Inverter ($-15\text{ V}$):** Implemented using an inverting topology on the **MC34063AG-S08-R** PWM controller without an external switch. The generator operating frequency is set by the capacitor $C_1 = 2.2\text{ nF}$ ($\approx 97.5\text{ kHz}$).

---

## 4. Mechanical Design & 3D Modeling

To protect the electronics and ensure usability, a two-part protective plastic enclosure was designed in **KOMPAS-3D** (assembly of parts using a tongue-and-groove system):

<p align="center">
  <img src="/mechanical/images/breadboard-power-qcpd-assembly(1).png" width="650" alt="3D Model of the Enclosure - View 1">
</p>

*The enclosure parts (top cover and bottom plate) are optimized for additive manufacturing using FDM 3D printing (print-ready `.stl` files can be found in the [`mechanical/enclosure/`](/mechanical/enclosure) folder).*

---

## 5. Lab Testing & Hardware Verification

The device was successfully soldered and tested:

<p align="center">
  <img src="/production/images/assembled-pcb-test.jpg" width="600" alt="Physical testing of the assembled prototype">
</p>

During verification using a digital oscilloscope, the operating stability of the inverter's internal oscillator and the continuous conduction mode of the power inductor were confirmed:

<p align="center">
  <img src="/electronics/verification/images/mc34063-c1-waveform.png" width="380" alt="C1 Waveform">
  &nbsp;&nbsp;&nbsp;&nbsp;
  <img src="/electronics/verification/images/mc34063-l1-waveform.png" width="380" alt="L1 Waveform">
</p>

* **C1 Waveform (left):** Confirms the converter's switching frequency of $97.5\text{ kHz}$ (within the allowable limit of $100\text{ kHz}$).
* **L1 Waveform (right):** Confirms the correct choice of $220\text{ }\mu\text{H}$ inductance, ensuring a stable continuous inductor current without entering discontinuous conduction mode.

---

## 6. Switch Configuration Tables

### 6.1. QC/PD Input Voltage Selection (Switch `SA2`)

| `CFG1` | `CFG2` | `CFG3` | Output Voltage `VBUS` |
| :---: | :---: | :---: | :---: |
| $1$ | $-$ | $-$ | **$5\text{ V}$** |
| $0$ | $0$ | $0$ | **$9\text{ V}$** |
| $0$ | $0$ | $1$ | **$12\text{ V}$** |
| $0$ | $1$ | $1$ | **$15\text{ V}$** |
| $0$ | $1$ | $0$ | **$20\text{ V}$** |

### 6.2. Breadboard Power Rail Commutation (Switches `SA1`, `SA3`)

| `SA1` Position | `SA3` Position | Voltage on Left Rail `X2` | Voltage on Right Rail `X3` |
| :---: | :---: | :---: | :---: |
| 1 | 1 | $+3.3\text{ V}$ | $-15\text{ V}$ |
| 0 | 1 | $+3.3\text{ V}$ | $+3.3\text{ V}$ |
| 0 | 0 | `VBUS` (from trigger) | `VBUS` (from trigger) |
| 1 | 0 | `VBUS` (from trigger) | $-15\text{ V}$ |

---

## 7. Project Navigation

The repository has a clear modular structure. Click on the links below to navigate to the section of interest and review the source files:

* 📁 **[`/electronics`](/electronics)** — Hardware section. Contains schematic and PCB source files in EasyEDA format, as well as the **[`verification`](/electronics/verification)** subfolder with detailed mathematical calculations for the converter components and test waveforms.
* 📁 **[`/mechanical`](/mechanical)** — Mechanical design and enclosure. Contains native parametric models of the enclosure parts in KOMPAS-3D (`.m3d`, `.a3d`) and ready-to-print `.stl` files for FDM 3D printing.
* 📁 **[`/production`](/production)** — Self-assembly and manufacturing files. Contains the Gerber files archive for PCB fabrication (`gerber.zip`), bill of materials (`BOM.xlsx`), and PDF drawings.
* 📁 **[`/docs`](/docs)** — Technical documentation. Contains a detailed description of the circuit physics, switch truth tables, and the **[`datasheets`](/docs/datasheets)** subfolder with original datasheets for the ICs used.

## License

Copyright (c) 2026 Airat Yalaletdinov

This source describes Open Hardware and is licensed under the CERN-OHL-P v2. 
You may redistribute and modify this source and make products using it under 
the terms of the CERN-OHL-P v2 (https://cern.ch/cern-ohl).

This source is distributed WITHOUT ANY EXPRESS OR IMPLIED WARRANTY, 
INCLUDING OF MERCHANTABILITY, SATISFACTORY QUALITY AND FITNESS FOR A 
PARTICULAR PURPOSE. Please see the CERN-OHL-P v2 for applicable conditions.
