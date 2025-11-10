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

By combining logic gates and flip-flops together (ignoring wires), we can approximate the total **core area**.

<img width="1078" height="797" alt="Image" src="https://github.com/user-attachments/assets/308f2883-6643-47af-87fe-a49b20312fe6" />

After arranging all cells, assume the layout measures **2 units × 2 units** → total area = **4 sq. units**.

<img width="955" height="835" alt="Image" src="https://github.com/user-attachments/assets/825b5d4b-5c4e-4fb0-b40e-aa82574a5773" />

#### Core and Die Definition

- **Die:** The complete semiconductor area where the circuit is fabricated.  
- **Core:** The region within the die that holds the main logic design.

<img width="846" height="751" alt="Image" src="https://github.com/user-attachments/assets/b01d197f-1b02-4a60-9e7f-eb1dcc96fe33" />


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

After partitioning, each block can be black-boxed and treated as an **independent IP**.  
This allows reuse across multiple designs.

<img width="1560" height="722" alt="Image" src="https://github.com/user-attachments/assets/b3835e3d-e00b-434b-8154-e67b1b63bf9d" />

Examples of pre-placed cells (IP blocks):
- Memory blocks  
- Clock-gating cells  
- Comparators  
- Multiplexers (MUX)

These are **placed manually** before automated placement.  
Their fixed positions are preserved by the placement tool — hence called **pre-placed cells**.

---

### 1.3 De-Coupling Capacitors

When logic gates switch states, transient current spikes occur due to capacitive charging/discharging.  
This causes a **voltage drop** across resistive and inductive elements (Rdd, Ldd), reducing the effective voltage seen by the circuit.

<img width="1367" height="801" alt="Image" src="https://github.com/user-attachments/assets/dd2e8631-8988-4088-9b8e-e03e89dceed4" />


To counter this, **decoupling capacitors (decaps)** are connected in parallel to supply the required instantaneous current.  
These capacitors discharge during switching and recharge afterward, maintaining voltage stability.

<img width="1328" height="829" alt="Image" src="https://github.com/user-attachments/assets/27d0140b-74e7-44e4-8062-cbd7148a1863" />


In layout, decaps are placed between functional blocks to ensure stable local power delivery.

<img width="1073" height="837" alt="Image" src="https://github.com/user-attachments/assets/d9ce0786-0834-41b3-ae18-0979f4b3f83e" />


---

### 1.4 Power Planning

Even with decaps, distant power supply points cause **IR drop** and **ground bounce**.  
When multiple lines switch simultaneously (e.g., a 16-bit bus), all capacitors charge/discharge through the same VDD and GND points — causing supply fluctuations.

<img width="1127" height="810" alt="Image" src="https://github.com/user-attachments/assets/52a81e3c-1120-408e-87c5-3c642ac21aa4" />

To mitigate this, power is distributed using multiple VDD and GND connections arranged in a **mesh structure**.

<img width="1114" height="851" alt="Image" src="https://github.com/user-attachments/assets/0a9218c0-e431-49b7-824a-cbd5aac3bf29" />

A **power mesh** ensures:
- Uniform current distribution  
- Minimal IR drop  
- Stable voltage across the die

<img width="1221" height="721" alt="Image" src="https://github.com/user-attachments/assets/c4a0f883-3517-464d-a487-b5df6488100d" />

---

### 1.5 Pin Placement and Logical Cell Placement Blockage

Pin placement defines where all inputs, outputs, and clock pins are located on the chip periphery.

<img width="1122" height="719" alt="Image" src="https://github.com/user-attachments/assets/7a16e776-1381-4a40-a00f-758fd74b3b75" />


Pins should be placed close to their corresponding logic blocks.  
Clock pins are typically **larger** to reduce resistance and delay, ensuring clean clock distribution.

When pins are placed, **placement blockages** are created around them.  
This prevents standard cells from occupying routing regions near I/O pads.

<img width="1221" height="758" alt="Image" src="https://github.com/user-attachments/assets/0227e100-c5d1-416f-8750-b19a549163b0" />

At this stage, the **floorplan** is ready for the **Placement and Routing (P&R)** phase.

