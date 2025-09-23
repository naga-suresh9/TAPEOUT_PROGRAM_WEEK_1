# 📅 Week 1 – Day 1: Verilog RTL Design & Synthesis

Welcome!  
Today, we explore **Verilog RTL design**, **simulation**, and **synthesis** using **Icarus Verilog**, **GTKWave**, and **Yosys** with the **Sky130 standard-cell library**.  
This guide also includes **common errors and fixes** for smoother workflow.

---

## 🗂️ Table of Contents

1️⃣ Simulator, Design & Testbench  
2️⃣ Installing Tools & Fixes  
3️⃣ Lab: Simulate a 2-to-1 Multiplexer  
4️⃣ Verilog Code: `good_mux.v`  
5️⃣ Introduction to Yosys & Gate Libraries  
6️⃣ Synthesis with Yosys  
7️⃣ Viewing Gate-Level Schematic & Simulation Output  
8️⃣ ✅ Summary  

---

# 1️⃣ Simulator, Design & Testbench

- 🖥️ **Simulator:** Checks functionality using input vectors  
- 📝 **Design:** Verilog RTL describing logic behavior  
- 🧪 **Testbench:** Provides inputs and verifies outputs  

> Ensures designs are verified before hardware implementation.

---

# 2️⃣ Installing Tools & Fixes

```bash
sudo apt update
sudo apt install iverilog gtkwave graphviz xdot python3-setuptools
````

⚠️ **Fixes / Notes:**

* `python3-distutils` removed → use `python3-setuptools` for xdot
* Graphviz + xdot required for schematic visualization

---

# 3️⃣ Lab: Simulate a 2-to-1 Multiplexer

1️⃣ Navigate to Verilog files:

```bash
cd ~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

2️⃣ Compile & run simulation:

```bash
iverilog good_mux.v tb_good_mux.v
./a.out
```

3️⃣ View waveform:

```bash
gtkwave tb_good_mux.vcd
```

---

# 4️⃣ Verilog Code: `good_mux.v`

```verilog
module good_mux (
    input i0, 
    input i1, 
    input sel, 
    output reg y
);
always @ (*) begin
    if(sel)
        y <= i1;
    else 
        y <= i0;
end
endmodule
```

💡 **Logic Explanation:**

* Inputs: `i0`, `i1` (data), `sel` (select)
* Output: `y`
* If `sel=1` → `y=i1`; else `y=i0`

---

# 5️⃣ Introduction to Yosys & Gate Libraries

* ⚡ Converts **RTL → gate-level netlist**
* 🛠️ Optimizes speed, area, power
* 🏭 Technology mapping uses library cells

**Why multiple gate flavors?**

* 🚀 Performance: fast vs low-power
* 💪 Drive strength: strong vs standard
* 📏 Area: compact vs regular
* 🔊 Signal integrity: noise/performance

> Yosys chooses the best gates automatically.

---

# 6️⃣ Synthesis with Yosys

```yosys
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog good_mux.v
synth -top good_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

⚠️ **Fixes / Notes:**

* Ensure `.lib` path is correct
* If `xdot` fails, use DOT-to-PNG:

```yosys
show -format dot -prefix good_mux
dot -Tpng good_mux.dot -o good_mux.png
```

---

# 7️⃣ Viewing Gate-Level Schematic & Simulation Output

### 7.1 🧪 GTKWave Output

* Shows the waveform of `good_mux` simulation
![Image](https://github.com/user-attachments/assets/f691661e-7a36-447b-ab13-15777912f177)

### 7.2 🏭 Yosys Gate-Level Schematic

* Shows the gate-level mapping of the multiplexer after synthesis

![Image](https://github.com/user-attachments/assets/3682d54d-f5a6-4965-a52e-14d106a2ba74)

> Both images help visualize the design behavior and hardware mapping.

---

# 8️⃣ ✅ Summary

* Learned **simulators, designs, testbenches**
* Ran **first Verilog simulation** and viewed waveforms
* Analyzed `good_mux.v` logic
* Synthesized design using **Yosys + Sky130 library**
* Fixed **common errors** (xdot, distutils, library path)
* Viewed **gate-level schematic** and **simulation waveform**

```
