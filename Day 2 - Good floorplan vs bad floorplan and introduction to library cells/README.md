# Sky130 Day 2 - Good Floorplan vs Bad Floorplan and Introduction to Library Cells

This document explains the core concepts of **chip floorplanning** in the physical design process.  
It covers utilization and aspect ratio, pre-placed cells, decoupling capacitors, power planning, and pin placement.  
All concepts are illustrated using the **Sky130 PDK** and the **OpenLANE flow**.

## 1. Chip Floor Planning Considerations

Floorplanning defines how logic blocks, memory, and power networks are arranged on a chip.  
It determines the **die size**, **core area**, and the **location of pre-placed cells**, ensuring optimal performance and efficient routing.

### 1.1 Utilization Factor and Aspect Ratio

In this section, we determine the **width** and **height** of the core and die — the first step of the physical design flow.

A **netlist** defines the connectivity between logic elements (e.g., flip-flops, AND/OR gates).  
Each standard cell is assumed to occupy **1 unit × 1 unit**.

So,  
**Area of one standard cell** = 1 sq. unit  
**Area of one flip-flop** = 1 sq. unit

* By combining logic gates and flip-flops together (ignoring wires), we can approximate the total **core area**.

<img width="1078" height="797" alt="Image" src="https://github.com/user-attachments/assets/308f2883-6643-47af-87fe-a49b20312fe6" />

#

* After arranging all cells, assume the layout measures **2 units × 2 units** → total area = **4 sq. units**.

<img width="955" height="835" alt="Image" src="https://github.com/user-attachments/assets/825b5d4b-5c4e-4fb0-b40e-aa82574a5773" />

#

#### Core and Die Definition

- **Die:** The complete semiconductor area where the circuit is fabricated.  
- **Core:** The region within the die that holds the main logic design.

<img width="846" height="751" alt="Image" src="https://github.com/user-attachments/assets/b01d197f-1b02-4a60-9e7f-eb1dcc96fe33" />

#

#### Utilization Factor and Aspect Ratio

  Utilization Factor = Area occupied by netlist / Total area of the core
     
     Utilization factor = 4*1sq.unit / 2unit *2unit
     
			= 4sq unit /  4sq unit

So, utilization factor = 1 (It means core has utilized all the area and no spane left)

Aspect Ratio = Height /  width =  2 unit /  2unit =  1

Whenever Aspect Ratio is 1 it signifies that chip is square shaped. When it is not 1 it means the chip is in rectangular shape.

---

### 1.2 Concept of Pre-Placed Cells

Let’s take a large combinational logic block consisting of many logic gates.  
We can divide it into smaller blocks (modules) for easier implementation.

<img width="1577" height="842" alt="Image" src="https://github.com/user-attachments/assets/28865a19-59f4-410a-a4ce-5fb0d7bf25f3" />

#

* After partitioning, each block can be black-boxed and treated as an **independent IP**.  
This allows reuse across multiple designs.

<img width="1560" height="722" alt="Image" src="https://github.com/user-attachments/assets/b3835e3d-e00b-434b-8154-e67b1b63bf9d" />

 Examples of pre-placed cells (IP blocks):
- Memory blocks  
- Clock-gating cells  
- Comparators  
- Multiplexers (MUX)

These are **placed manually** before automated placement.  
Their fixed positions are preserved by the placement tool — hence called **pre-placed cells**.

#

### 1.3 De-Coupling Capacitors

When logic gates switch states, transient current spikes occur due to capacitive charging/discharging.  
This causes a **voltage drop** across resistive and inductive elements (Rdd, Ldd), reducing the effective voltage seen by the circuit.

<img width="1367" height="801" alt="Image" src="https://github.com/user-attachments/assets/dd2e8631-8988-4088-9b8e-e03e89dceed4" />

#

* To counter this, **decoupling capacitors (decaps)** are connected in parallel to supply the required instantaneous current.  
These capacitors discharge during switching and recharge afterward, maintaining voltage stability.

<img width="1328" height="829" alt="Image" src="https://github.com/user-attachments/assets/27d0140b-74e7-44e4-8062-cbd7148a1863" />

#

* In layout, decaps are placed between functional blocks to ensure stable local power delivery.

<img width="1073" height="837" alt="Image" src="https://github.com/user-attachments/assets/d9ce0786-0834-41b3-ae18-0979f4b3f83e" />


---

### 1.4 Power Planning

* Even with decaps, distant power supply points cause **IR drop** and **ground bounce**.  
When multiple lines switch simultaneously (e.g., a 16-bit bus), all capacitors charge/discharge through the same VDD and GND points — causing supply fluctuations.

<img width="1127" height="810" alt="Image" src="https://github.com/user-attachments/assets/52a81e3c-1120-408e-87c5-3c642ac21aa4" />

#

* To mitigate this, power is distributed using multiple VDD and GND connections arranged in a **mesh structure**.

<img width="1114" height="851" alt="Image" src="https://github.com/user-attachments/assets/0a9218c0-e431-49b7-824a-cbd5aac3bf29" />

# 

A **power mesh** ensures:
- Uniform current distribution  
- Minimal IR drop  
- Stable voltage across the die

<img width="1221" height="721" alt="Image" src="https://github.com/user-attachments/assets/c4a0f883-3517-464d-a487-b5df6488100d" />

---

### 1.5 Pin Placement and Logical Cell Placement Blockage

Pin placement defines where all inputs, outputs, and clock pins are located on the chip periphery.

<img width="1122" height="719" alt="Image" src="https://github.com/user-attachments/assets/7a16e776-1381-4a40-a00f-758fd74b3b75" />

#

* Pins should be placed close to their corresponding logic blocks.  
* Clock pins are typically **larger** to reduce resistance and delay, ensuring clean clock distribution.

* When pins are placed, **placement blockages** are created around them.  
This prevents standard cells from occupying routing regions near I/O pads.

<img width="1221" height="758" alt="Image" src="https://github.com/user-attachments/assets/0227e100-c5d1-416f-8750-b19a549163b0" />

---

#### 1.6 Steps to Run Floorplan Using OpenLANE

This section details the process of running and analyzing a floorplan for the `picorv32a` design using the **OpenLANE** flow.  
The goal is to understand the configuration parameters, execute the floorplan, and interpret the generated layout data.

Before executing the floorplan, review essential parameters such as:
- Chip size  
- Utilization factor  
- Aspect ratio  
- Vertical and horizontal metal layers  

These parameters act as configuration switches influencing the physical characteristics of the layout.

```bash
# Change the directory to the configuration folder
cd Desktop/work/tools/openlane_working_dir/openlane/configuration

# Open the configuration README file
less README.md
````

<img width="1600" height="1200" alt="Image" src="https://github.com/user-attachments/assets/a7f79e7b-8c4a-4227-b8c9-f1c95f0191db" />

#

* This file lists synthesis-related variables such as maximum fan-out, library settings, and threshold limits, which serve as key switches for different stages of the design process.

<img width="1600" height="1200" alt="Image" src="https://github.com/user-attachments/assets/b441941e-a950-4894-bf74-36acbe2165b4" />

---

#### Floorplan Configuration Parameters

The floorplan parameters are defined in a TCL script that specifies default layout and geometry properties.

```bash
# Open the floorplan configuration script
less floorplan.tcl
```

This file includes variables such as **core utilization**, **aspect ratio**, **IO placement**, and other layout constraints.
These default parameters guide OpenLANE in generating the initial die and core boundary.

<img width="1600" height="1200" alt="Image" src="https://github.com/user-attachments/assets/7781e3d3-c636-4836-bbe8-b3625c69cf03" />

---

####  Running the Floorplan for the `picorv32a` Design

After reviewing the configuration, the floorplan can be executed interactively using OpenLANE.

```bash
# Move to the OpenLANE root directory
cd Desktop/work/tools/openlane_working_dir/openlane

# Launch the OpenLANE Docker environment
docker

# Start OpenLANE in interactive mode
./flow.tcl -interactive

# Load OpenLANE packages
package require openlane 0.9

# Prepare the design workspace for 'picorv32a'
prep -design picorv32a

# Run synthesis
run_synthesis

# Execute the floorplan stage
run_floorplan
```

These commands initiate the OpenLANE environment, prepare the design, and execute both synthesis and floorplan operations.
The results include generated DEF and configuration files representing the physical structure of the design.

<img width="1600" height="1200" alt="Image" src="https://github.com/user-attachments/assets/0abf6d38-f9e0-49c8-8b1a-46049f190ddb" />
<img width="1600" height="1200" alt="Image" src="https://github.com/user-attachments/assets/e21ec731-7a6c-40ea-bc66-b25052024f13" />

---

#### 1.7 Review of Floorplan Files

Once the floorplan completes, OpenLANE produces a set of reports and configuration files within the design directory.

```bash
# Navigate to the design run directory
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-10_08-30/

# Review the configuration file
less config.tcl
```

The configuration file provides details of all variables used during synthesis and floorplanning, including die dimensions, routing layers, and utilization factors.

<img width="1600" height="1200" alt="Image" src="https://github.com/user-attachments/assets/118341fd-b929-4c94-b333-327dadcfbae1" />

#

#### Accessing the Floorplan DEF File

The **DEF (Design Exchange Format)** file contains geometric data defining the die area and component placement.

```bash
# Navigate to the floorplan results directory
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-10_08-30/results/floorplan

# Open the generated floorplan DEF file
less picorv32a.floorplan.def
```

This file defines the die boundaries, placement blockages, pin locations, and other layout elements necessary for subsequent stages of physical design.

<img width="1600" height="1200" alt="Image" src="https://github.com/user-attachments/assets/68443b8c-283a-4ca8-b106-254ac1763648" />
<img width="1600" height="1200" alt="Image" src="https://github.com/user-attachments/assets/5a4d6579-2cc6-484f-9a6d-e8f8938a5d0b" />

#

#### Interpreting DEF File Data

Die Area : (0 0)(660685 671405)
Units Distance micron 1000

#### Understanding the DEF Information

- **(0, 0)** → Represents the **lower-left corner** of the die.  
- **(660685, 671405)** → Represents the **upper-right corner** of the die.  
- **Units Distance micron 1000** → Indicates that **1 micron = 1000 database units.**

These values define the **physical dimensions** of the die in the layout coordinate system.

#

#### Converting Database Units to Microns

| Parameter | Database Units | Conversion Formula | Result (µm) |
|------------|----------------|--------------------|--------------|
| Width | 660685 | 660685 ÷ 1000 | **660.685 µm** |
| Height | 671405 | 671405 ÷ 1000 | **671.405 µm** |

#

#### Calculating Die Area

 Area of die in microns = Die width in microns * Die hieght in microns
                        = (660685/1000) * (671405/1000)
                        = 443587.212425 square microns

---

# 2. Library Binding and Placement

## 2.1 Netlist Binding and Initial Place Design

**Netlist Binding with Physical Cells:**  
The process begins by binding the logical netlist to its corresponding physical cells. A gate’s shape in a schematic—such as a triangle for a NOT gate—is merely symbolic. In the physical layout, every gate, flip-flop, and block is represented as a rectangular cell with defined width and height. Assigning physical dimensions to these logical components creates a realistic representation for placement on silicon.

All physical cells, including their geometric and electrical characteristics, are stored in a **library**. This library acts as a repository of standard cells, each with specific properties such as size, shape, delay, and drive strength.  
A library may include multiple versions of the same cell to meet different timing and area requirements—larger cells typically have lower resistance and faster switching speeds but occupy more space. Designers select suitable cells depending on timing constraints and available chip area.

<img width="1218" height="795" alt="Image" src="https://github.com/user-attachments/assets/56443486-734c-465d-897e-59f8c15ebb47" />

**Placement:**  
Once physical dimensions are assigned, placement involves positioning these cells on the floorplan. The goal is to ensure connectivity between cells matches the netlist, while maintaining minimal wire length and delay. Placement also respects fixed or pre-placed components and ensures no overlap between cells.

In this stage, the logical connectivity from the design is mapped to the physical layout, arranging cells close to their related input and output pins to achieve optimal performance and timing.

<img width="1217" height="586" alt="Image" src="https://github.com/user-attachments/assets/dcb4b6cf-a01e-4c55-bf6b-917b29c49fd3" />

---

## 2.2 Optimize Placement Using Estimated Wire-Length and Capacitance

**Optimized Placement:**  
Optimization aims to reduce wire-length and associated capacitance. Long interconnections result in higher resistance and capacitance, increasing signal delay and degrading signal integrity.  
To mitigate this, **repeaters (buffers)** are inserted between long interconnections. These repeaters regenerate the signal strength, maintaining timing accuracy and signal quality across long paths. However, adding more buffers increases area utilization, so a balance between performance and area must be maintained.

This optimization ensures efficient signal propagation, reducing RC delays while preserving design compactness.

<img width="1221" height="599" alt="Image" src="https://github.com/user-attachments/assets/cf779949-d16f-448b-b5fd-dc27d9c9dae9" />

---

## 2.3 Final Placement Optimization

After initial optimization, further fine-tuning is performed to address any residual timing issues. Buffers may still be added where long distances remain between key components such as gates and flip-flops.

At this stage, **timing analysis** is performed using ideal clock assumptions to verify the effectiveness of placement. The objective is to validate whether all timing constraints—such as setup and hold times—are satisfied. Adjustments may be made to placement or buffering based on this analysis to achieve the best overall performance.

<img width="1218" height="588" alt="Image" src="https://github.com/user-attachments/assets/4546377b-e4a2-403b-8764-210c42c39e56" />

---

## 2.4 Need for Libraries and Characterization

An integrated circuit design flow progresses through several stages, including **Logic Synthesis**, **Floorplanning**, **Placement**, **Clock Tree Synthesis (CTS)**, **Routing**, and **Static Timing Analysis (STA)**.  
Across all these stages, the use of **standard cell libraries** is crucial.

- **Logic Synthesis:** Converts RTL code into a gate-level netlist using available standard cells.
- **Floorplanning:** Defines the die and core areas to accommodate the synthesized design.
- **Placement:** Positions logic cells to meet timing and area constraints.
- **CTS:** Ensures the clock reaches all sequential elements simultaneously by balancing skew.
- **Routing:** Connects all cells based on timing and congestion information.
- **STA:** Analyzes timing to verify setup, hold times, and maximum frequency.

Each of these stages relies on the library’s physical and electrical data—such as cell delay, transition times, and power consumption—highlighting the importance of proper **library characterization**.

<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/2bb0ae26-c970-406b-912e-150c89c9fa5b" />

---

## 2.5 Congestion-Aware Placement Using RePlAce

In congestion-aware placement, the focus shifts from timing to **routing congestion**. The goal is to ensure sufficient routing resources are available by minimizing overlaps and local density peaks.

Placement occurs in two stages:

1. **Global Placement:** Roughly positions all cells to minimize overall wire-length without enforcing exact alignment.
2. **Detailed Placement:** Legalizes the global placement by aligning cells properly within placement rows, removing overlaps, and ensuring design rules are met.

This two-step approach ensures efficient utilization of area, balanced wire-length, and reduced routing congestion, ultimately improving timing closure and manufacturability.

---

# 3. Cell Design and Characterization Flows

## 3.1 Inputs for Cell Design Flow

In cell design, logic components such as gates, flip-flops, and buffers are referred to as **standard cells**. These cells are organized within a **library**, which contains multiple variants differing in drive strength and physical size.

The **inputs** required for cell design include:

- **PDKs (Process Design Kits):** Contain technology files defining design rules and parameters.  
- **DRC and LVS Rules:** Define geometric and connectivity constraints to ensure layout correctness.  
- **SPICE Models:** Provide transistor-level behavior for accurate circuit simulation.  
- **Library and User Specifications:** Define functional, electrical, and timing requirements.  

These inputs form the foundation for accurate and manufacturable standard cell design.

<img width="1223" height="776" alt="Image" src="https://github.com/user-attachments/assets/00de0349-1b6b-49de-a8cb-0dcaef4144e6" />

---

## 3.2 Circuit Design Step

The **circuit design stage** involves creating transistor-level implementations of logic functions. It includes two main tasks:

1. **Functional Implementation:** Building the circuit using PMOS and NMOS transistors to achieve the desired logic function.  
2. **Transistor Modeling:** Adjusting device parameters to meet timing and drive strength targets specified in the library.

The height of a cell is determined by the distance between power and ground rails, while its width depends on performance requirements.  
The key outputs from circuit design include:

- **CDL (Circuit Description Language) File**  
- **GDSII Layout File**  
- **LEF File**  
- **Extracted SPICE Netlist (.cir)**

<img width="1224" height="727" alt="Image" src="https://github.com/user-attachments/assets/9e3b7ec2-70bc-4f95-8bc4-8f7678e7bb4c" />

---

## 3.3 Layout Design Step

**Layout design** converts the transistor-level circuit into a physical representation suitable for fabrication.

The process involves:
- Implementing the logic using PMOS and NMOS transistors.  
- Creating corresponding **network graphs** for PMOS and NMOS connections.  
- Deriving the **Euler path** to minimize diffusion breaks and optimize layout compactness.  
- Developing a **stick diagram** based on the Euler path to visualize the transistor layout.  
- Translating the stick diagram into a **complete layout**, ensuring adherence to design rules (DRC).  

After completing the layout, **parasitic extraction** is performed to obtain realistic resistance and capacitance values. These parasitics are used during **cell characterization** for accurate timing and power analysis.

<img width="1225" height="730" alt="Image" src="https://github.com/user-attachments/assets/fe2a62ec-2c4b-4eba-a022-cee6d8c4e0ae" />

---

## 3.4 Typical Characterization Flow

The **characterization process** determines the electrical behavior of the designed cell. It involves several sequential steps:

1. Reading the device model files.  
2. Importing the extracted SPICE netlist.  
3. Defining buffer behavior and input conditions.  
4. Reading subcircuit definitions (e.g., inverters).  
5. Attaching required power supplies.  
6. Applying stimulus waveforms.  
7. Adding output load capacitance.  
8. Running simulations using commands such as `.tran` (transient) or `.dc` (DC).  

All configuration data is provided to a characterization software tool (e.g., **GUNA**), which outputs **timing, power, and noise models** used for standard cell libraries.

<img width="1220" height="668" alt="Image" src="https://github.com/user-attachments/assets/e52eea70-b19f-4a72-855c-586f80b5b498" />

---

# 4. General Timing Characterization Parameters

## 4.1 Timing Threshold Definitions

Timing thresholds define the voltage levels used to measure rise and fall times in digital waveforms. Typical thresholds include:

- **Slew_low_rise_thr**: Usually around 20–30% of the signal voltage.  
- **Slew_high_rise_thr**: Typically around 70–80%.  
- **Slew_low_fall_thr** and **Slew_high_fall_thr**: Represent similar points on the falling edge.

For delay measurements, thresholds are generally taken at 50% of the signal swing:

- **in_rise_thr** and **in_fall_thr** (input signal).  
- **out_rise_thr** and **out_fall_thr** (output signal).  

Accurate threshold selection is crucial, as improper values may cause incorrect or negative delay calculations.

---

## 4.2 Propagation Delay and Transition Time

Once threshold definitions are established, key timing parameters are computed:

- **Propagation Delay** = Time(out_thr) – Time(in_thr)  
  - Represents the time difference between the 50% crossing points of input and output waveforms.  
  - Negative delay indicates incorrect threshold selection or signal overlap and must be avoided.  

- **Transition Time (Slew)**  
  - For rising edge: `time(slew_high_rise_thr) – time(slew_low_rise_thr)`  
  - For falling edge: `time(slew_high_fall_thr) – time(slew_low_fall_thr)`  

These parameters directly influence setup and hold time analysis, timing closure, and overall circuit performance.

<img width="1220" height="583" alt="Image" src="https://github.com/user-attachments/assets/d64e09a1-cd74-49f9-a2e4-2ae30d370293" />

