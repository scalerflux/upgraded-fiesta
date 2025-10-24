# Week 5
To set up the OpenROAD Flow Scripts environment and execute the Floorplan and Placement stages of the physical design flow.

﻿﻿﻿﻿
## Installation Steps

### 1. Repo Clone
```
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
cd OpenROAD-flow-scripts
```
---
<img width="1343" height="645" alt="Screenshot 2025-10-23 at 12 03 38 PM" src="https://github.com/user-attachments/assets/3391c9ff-c85f-47c0-9bb1-f8d307e12b87" />

---

### 2. Dependency installation 

```
sudo ./setup.sh
```
Installs all the prerequisite tools/libraries needed to BUILD OpenROAD
---
<img width="1253" height="653" alt="Screenshot 2025-10-23 at 3 12 25 PM" src="https://github.com/user-attachments/assets/2b82ea2a-4ede-4eba-8c5c-2d6cfb1dc515" />

---

### 3. Build OpenRoad

```
./build_openroad.sh --local
```
This step compiles (builds) all the tools from that source code so you can actually use them.
What Gets Built:

* OpenROAD - The main tool that does floorplan, placement, routing, etc.
* Yosys - The synthesis tool (converts Verilog to gates) - you've used this before!
* Other utilities - ABC, KLayout, Magic, etc.
---
<img width="1124" height="793" alt="Screenshot 2025-10-23 at 7 56 26 PM" src="https://github.com/user-attachments/assets/b4ea9260-3d2d-4e2d-926f-696e6676d291" />
<img width="1124" height="290" alt="Screenshot 2025-10-23 at 7 57 05 PM" src="https://github.com/user-attachments/assets/e8af42b1-8560-43e8-961e-2d1625677a51" />

---

﻿**The openroad file exists and is 97MB**

***

### 4. Verify 
 ---
 <img width="1124" height="574" alt="Screenshot 2025-10-23 at 8 01 55 PM" src="https://github.com/user-attachments/assets/0243ada2-782b-4aa3-ae36-504a4ff6a98a" />

 ---

### 5. Run openroad flow
```
make
```
* Runs the full RTL-to-GDSII flow on the "gcd" test design
* Synthesis ➡️ Floorplan ➡️ Placement ➡️ Clock Tree Synthesis ➡️ Routing
---  
<img width="1046" height="669" alt="Screenshot 2025-10-24 at 10 27 17 AM" src="https://github.com/user-attachments/assets/77c80b12-42c4-4e70-952d-54be571753b3" />

---

### 6. View the complete design
   
* Opens the OpenROAD GUI
* Shows the complete routed chip with all wires connected
```
make gui_final
```
---
<img width="1546" height="1069" alt="Screenshot 2025-10-24 at 10 30 13 AM" src="https://github.com/user-attachments/assets/930fb797-eb6e-4835-a6ea-6248d72fbb2a" />

---
* Chip layout with coloured metal layers
* Stnadard cells arranged in rows


We've just verified the full flow works. Now we need to run ONLY the floorplan and placement stages


### 7. Run Synthesis (Required Before Floorplan)

Before floorplan, we need synthesis (converts Verilog to gates):
```
make synth
```

* **Input**: RTL Verilog code.
* **Output**: A gate-level netlist, which is the required input for the floorplan stage.
---
<img width="1257" height="560" alt="Screenshot 2025-10-24 at 10 35 37 AM" src="https://github.com/user-attachments/assets/9dd20f18-f1ab-46a8-8af5-66f70ca0f9ee" />

---

### 8. Run Floorplan 
```
make floorplan
```
What this does:
* 🗺️ **Creates the chip boundary** (die area) and the core area for placing standard cells.
* 📍 **Places the I/O pins** around the periphery of the core.
* 📏 **Defines the standard cell rows** where the logic gates will be placed.
* ⚡ **Builds the Power Delivery Network (PDN)** to supply power to all parts of the chip.
---  
<img width="1105" height="1003" alt="Screenshot 2025-10-24 at 10 40 10 AM" src="https://github.com/user-attachments/assets/ac02b47f-393d-41be-866b-d818ae116eee" />

---

### 9. View the Floorplan in GUI

Now let's visualize what the floorplan created:
```
make gui_floorplan
```
What this does:
* Opens the GUI showing ONLY the floorplan stage
* You'll see:

* The outer **die boundary** and the inner **core area**.
* Empty blue horizontal lines, which are the **standard cell rows**.
* A grid of pink/purple lines, which is the **power grid**.
* **I/O pins** placed around the edges.
---
<img width="1383" height="1072" alt="Screenshot 2025-10-24 at 10 44 18 AM" src="https://github.com/user-attachments/assets/f422e8ef-9a77-453f-a158-b07d2557c4c4" />

---
What's Visible:

* **Pink/purple horizontal bars** - Power delivery network (PDN) stripes
* **Blue horizontal lines** - Standard cell rows (empty, waiting for cells)
* **Die boundary** - The outer rectangle defining chip size
* **Core area** - The inner area where cells will be placed
* **Metal layers** shown in the left panel (metal1-10, via layers)

> **Key Observation:** At this stage, the rows are empty. No standard cells have been placed yet. This is the correct output for the floorplan stage.

### 10. Run Placement 
```
make place
```

What this does:

* **Global placement** - Roughly positions all cells
* **Detailed placement** - Fine-tunes cell positions to legal locations
* Optimizes for timing and wirelength
---
﻿<img width="984" height="509" alt="Screenshot 2025-10-24 at 10 53 40 AM" src="https://github.com/user-attachments/assets/ca09383f-c144-4c62-aaad-3369b46253d1" />

---

﻿Key results from the screenshot:
* Detailed placement completed - "report_design_area" section shows final metrics
* Design area: 668 um² with 58% utilization
* **No violations** - "Found 0 placement violations and 0 padding violations"
* **Timing improvements** - HPWL (Half-Perimeter Wire Length) optimized through multiple passes
* Cell flipping performed for better routing compatibility

### 11. View the Placement in GUI

Now let's see the cells actually placed:
```
make gui_place
```

What this does:
* Opens GUI showing the placement stage
* You'll see:

  * All the floorplan infrastructure (rows, power grid)
  * PLUS actual standard cells placed in the rows
  * Cells arranged optimally for timing and routing
---
<img width="1373" height="1067" alt="Screenshot 2025-10-24 at 10 57 09 AM" src="https://github.com/user-attachments/assets/30337367-eb5b-4caf-bcf8-4eedda92d00c" />

---

**Comparison - Floorplan vs Placement:**

* Before (Floorplan): Empty blue rows waiting for cells
* After (Placement): Rows now FILLED with colorful standard cells

What you're seeing:
* **Blue/colored rectangles** - Individual standard cells (gates) now placed
* **Pink horizontal stripes** - Power delivery network (same as before)
* **Dense cell packing** - Cells optimally arranged in rows
* **Yellow vertical line** - Channel separator in the middle
* **I/O pins** - Small markers on edges (top and sides)
* **This shows the 58% utilization** - cells occupy about 60% of the available core area.
​

### 12. Key netrics n  Placement Report

Let's check the detailed placement metrics:
Shows the placement report with area, utilization, timing info
​
---
<img width="1422" height="1168" alt="Screenshot 2025-10-24 at 11 16 03 AM" src="https://github.com/user-attachments/assets/0ffea6e7-4c08-49e3-b7f5-02f72543b0de" />
<img width="933" height="718" alt="Screenshot 2025-10-24 at 11 21 07 AM" src="https://github.com/user-attachments/assets/edc09931-5990-4b45-91d6-cb4a4757b675" />

---

 **Final HPWL**: 2714.1 µm (a measure of total wire length).
* **HPWL Improvement**: -10.9% (wire length was successfully reduced during optimization).
* **Design Area**: 668 µm² with **58% utilization**.

**Placement Validation (All Passed ✓):**

* ✅ **0 overlapping cells**
* ✅ **0 spacing violations**
* ✅ **0 row alignment problems**
* ✅ **0 site alignment problems**

**Optimization Steps Performed:**

* **Global Swaps**: Achieved a 4.04% improvement in wirelength.
* **Cell Flipping**: 171 cells were flipped for better routing.
﻿

