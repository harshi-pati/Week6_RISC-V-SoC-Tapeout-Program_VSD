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

---

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

---

#### Interpreting DEF File Data

Die Area : (0 0)(660685 671405)
Units Distance micron 1000

#### Understanding the DEF Information

- **(0, 0)** → Represents the **lower-left corner** of the die.  
- **(660685, 671405)** → Represents the **upper-right corner** of the die.  
- **Units Distance micron 1000** → Indicates that **1 micron = 1000 database units.**

These values define the **physical dimensions** of the die in the layout coordinate system.

---

#### Converting Database Units to Microns

| Parameter | Database Units | Conversion Formula | Result (µm) |
|------------|----------------|--------------------|--------------|
| Width | 660685 | 660685 ÷ 1000 | **660.685 µm** |
| Height | 671405 | 671405 ÷ 1000 | **671.405 µm** |

---

#### Calculating Die Area

 Area of die in microns = Die width in microns * Die hieght in microns
                        = (660685/1000) * (671405/1000)
                        = 443587.212425 square microns



