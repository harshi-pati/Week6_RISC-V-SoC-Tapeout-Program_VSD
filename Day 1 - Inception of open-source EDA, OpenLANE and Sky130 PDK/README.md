
# 🧠 Sky130 Day 1 – Inception of Open-Source EDA, OpenLANE, and Sky130 PDK


## **Overview**

The **Sky130 open-source PDK** and **OpenLANE ASIC design flow** have revolutionized modern chip design by eliminating dependence on closed, expensive EDA tools.
This framework enables students, researchers, and startups to design, simulate, and tape-out silicon chips using open-source software and a free PDK provided by SkyWater Technology Foundry.

The **Sky130 process node** (130 nm CMOS) may be legacy, but it remains widely used for its cost-effectiveness, tool maturity, and suitability for education and prototyping.

## **1. How to Talk to Computers**

### **1.1 Introduction to QFN-48 Package, Chip, Pads, Core, Die, and IPs**

* The **QFN-48** (Quad Flat No-lead, 48 pins) package is a compact surface-mount standard that allows efficient chip integration onto printed circuit boards (PCBs).
* The **chip architecture** includes:

  * **Die core** – contains the actual silicon logic circuits.
  * **Pads** – act as interfaces between internal signals and external package pins.
  * **Package pins** – provide mechanical and electrical connection to the PCB.
* The **System-on-Chip (SoC)** integrates multiple **IP blocks** such as memory, processors, and I/O controllers within a single die.
* Proper **pad arrangement**, **bonding wire routing**, and **substrate design** ensure minimal signal interference, reduced noise, and enhanced device reliability.

<img width="1546" height="856" alt="Image" src="https://github.com/user-attachments/assets/0c364208-7f8f-4afe-a3b0-fcabd41bf197" />

---

### **1.2 Introduction to RISC-V**

* **RISC-V** is an open and modular **Instruction Set Architecture (ISA)** that provides flexibility and extensibility for SoC development.
* Its simplicity allows designers to create **custom CPUs** or **hardware accelerators** that meet specific performance or power needs.
* RISC-V supports a clean separation between:

  * **Base instructions** (integer operations)
  * **Optional extensions** (floating-point, atomic, vector, etc.)
* Its open nature aligns perfectly with open-source ASIC design using OpenLANE and Sky130 PDK.

---

### **1.3 From Software Applications to Hardware**

* Software algorithms are mapped into **hardware logic** using **Hardware Description Languages (HDLs)** like **Verilog** or **VHDL**.
* These describe how data moves through:

  * **Data paths** – performing operations such as addition, multiplication, shifting.
  * **Control paths** – orchestrating operations via state machines and timing.
* The **OpenLANE flow** translates the RTL (Register-Transfer Level) design into a **physical layout** (GDSII), ready for fabrication.
* This involves clocking, synchronization, timing constraints, and physical placement rules.


<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/4a057464-8b5a-4246-9572-378de00bf8ef" />


## **2. SoC Design and OpenLANE**


### **2.1 Introduction to All Components of Open-Source Digital ASIC Design**

* **RTL IPs:** Behavioral representations of logic written in Verilog/VHDL.
* **EDA Tools:** Software for synthesis, placement, routing, and verification.
* **PDK (Process Design Kit):** Provides foundry-specific models, rules, and device libraries.
* These components together form a complete **ASIC flow** — from RTL coding to a manufacturable chip layout.

<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/40f45d27-49b5-4c8b-8bb4-221346eec33b" />

---

### 💡 **Process Design Kit (PDK) Explanation**

* The **PDK** defines all the physical and electrical characteristics of the semiconductor manufacturing process.
* Key PDK files include:

  * **LEF** (Library Exchange Format): Describes cell geometries for placement and routing.
  * **LIB** (Timing Library): Provides timing, area, and power data for synthesis and STA.
  * **SPICE Models:** Device electrical characteristics for simulation.
  * **GDSII Files:** Final layout representation for fabrication.
* The PDK bridges design and fabrication, ensuring every drawn layout complies with the foundry’s process rules.

---

### **2.2 Simplified RTL2GDS Flow**

The **RTL-to-GDSII** process converts digital logic (RTL) into a physical chip layout.
OpenLANE automates this process through several major stages:

#### **a) Synthesis**

* Converts RTL into a **gate-level netlist** using standard cells from the library.
* Optimizes timing, area, and power based on technology constraints.

#### **b) Floor and Power Planning**

* Defines chip dimensions, placement boundaries, and **power distribution networks (PDN)**.
* Adds **power rings** and **decoupling capacitors** to stabilize voltage and prevent latch-up.
* Positions macros and I/O cells to maintain signal integrity.

#### **c) Placement**

* Performs **global** and **detailed placement** to distribute cells evenly.
* Ensures minimal routing congestion and optimal timing.

#### **d) Clock Tree Synthesis (CTS)**

* Builds a balanced **clock distribution network** using buffers, H-trees, or mesh structures.
* Reduces **clock skew** and maintains synchronous operation.

#### **e) Routing**

* Connects logic gates and macros using multiple **metal layers** (odd layers → horizontal, even → vertical).
* Controls **signal integrity**, **cross-talk**, and **via density**.

#### **f) Sign-Off**

* **Physical Verification:** DRC (Design Rule Check) and LVS (Layout vs Schematic).
* **Timing Verification:** STA (Static Timing Analysis) ensures timing closure.
* Confirms the design meets all fabrication constraints.

<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/d7292c60-ce5a-4cd4-826c-7c89e7c3d31e" />

---

### 💡 **OpenLANE Platform Overview**

* **OpenLANE** is an **open-source, automated RTL-to-GDSII flow** built on OpenROAD and Sky130 PDK.
* Operates in two modes:

  * **Autonomous mode:** Fully automatic flow.
  * **Interactive mode:** User-controlled, step-by-step execution.
* Supports **Design Space Exploration (DSE)** to test multiple configurations for area, power, and timing trade-offs.
* **Containerized environment** (Docker) ensures consistent reproducibility across systems.
* Integrates key tools:

  * Yosys (Synthesis)
  * OpenROAD (P&R)
  * Magic (DRC/LVS)
  * Netgen (LVS)
  * KLayout (Visualization)

---

### **2.3 Introduction to OpenLANE and Strive Chipsets**

* **Strive SoC** family represents real-world examples of silicon chips fabricated using OpenLANE and Sky130.
* Integrates IPs like **SRAM**, **RISC-V cores**, and **Digital-to-Analog Converters (DACs)**.
* Developed in collaboration with **Efabless**, **Google**, and the **OpenROAD Project**.
* Demonstrates the practicality and success of open-source ASIC design, including real silicon tape-outs.

---

### 💡 **Strive SoC Family and Example Chips**

* Each Strive SoC features:

  * A **RISC-V CPU core**
  * Embedded **OpenRAM SRAM blocks**
  * **DFT (Design for Test)** logic
  * On-chip **analog IPs**
* Fabricated using **Sky130 PDK** via open multi-project wafer (MPW) runs.
* Visual layouts and die photos show real hardware results from fully open-source flows.

<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/853e9493-1784-41e5-b65c-f93fb1a3c70e" />

---


### 💡 **Challenges with Open-Source EDA**

* **Qualification and calibration issues** between open tools and commercial flows.
* **Toolchain fragmentation** due to different versions and dependencies.
* **Limited analog and mixed-signal support** in open-source ecosystems.
* **Solutions:**

  * Containerized flows for stability.
  * Unified projects (OpenROAD, OpenLANE) for integration.
  * Collaborative community development to enhance features and coverage.

---

### 💡 **Market and Technology Node Relevance**

* Despite being older, the **130nm node** remains a key player in industrial and academic use.
* Benefits include:

  * **Lower cost** for prototyping and small-volume runs.
  * **Sufficient performance** for IoT, embedded, and sensor applications.
  * **Tool maturity** and **wide open-source support.**
* Global data shows consistent production volumes for 130nm and 180nm nodes, proving their continued importance.

---
### **2.4 Introduction to OpenLANE Detailed ASIC Design Flow**

