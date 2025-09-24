# 🚀✨ Day 2 – RTL Design Workflow: Libraries, Synthesis Strategies & Flip-Flop Implementation  

This day moves beyond simple RTL coding and into the practical tool flow.  
You’ll learn 📚 what the timing library is, 🔀 how hierarchical and flat synthesis differ, and 📝 how to write flip-flops correctly so they synthesize to standard cells without surprises.

---

## 1️⃣ ⏳ Timing Libraries in the Open PDK Flow  

### 📝 What is a Timing Library?  
A timing library (`.lib`) is a text database 📄 that tells synthesis tools how fast ⚡ and how much power 🔋 a standard cell uses under specific conditions. It contains:  
- 🧩 **Cell definitions** (e.g., NAND2, DFF)  
- 🎯 **Pin functions and direction**  
- ⏱ **Propagation delays, setup/hold constraints**  
- 🔋 **Dynamic & leakage power data**

The synthesis engine (Yosys + ABC) reads this information to choose the right cell sizes to meet timing ⌛.

### 🏭 SKY130 PDK Overview  
The SkyWater SKY130 open-source PDK gives you:
- 🗂 Layout and symbol data for 130 nm CMOS standard cells  
- 🕒 Liberty timing and power models for multiple process corners  
- 🗺 LEF/DEF for physical design  
- 🔬 SPICE models for transistor simulation  

### 🔎 Decoding the File Name `sky130_fd_sc_hd__tt_025C_1v80.lib`  
- 🏗 **sky130_fd_sc_hd**: High-density standard cell library  
- ⚙️ **tt**: Typical process corner  
- 🌡 **025C**: Characterized at 25 °C  
- 🔌 **1v80**: Core voltage 1.8 V  

### 🖥 Opening the File  
You can open `.lib` files with any text editor. On Ubuntu:
```bash
sudo apt install gedit
gedit sky130_fd_sc_hd__tt_025C_1v80.lib
````

📷 **Screenshot 1: Opening the SKY130 `.lib` file**
![📂 Opening .lib file](images/sky130_lib_file.png)

---

## 2️⃣ 🧩 Synthesis Styles: Hierarchical vs. Flattened

### 🗂 Hierarchical Synthesis

* 📌 **Meaning:** Each RTL module remains separate.
* ⚙️ **How Tools Handle It:** Yosys uses the `hierarchy` command to analyze modules one by one.
* 🎯 **When Useful:** Big projects, IP reuse, fast incremental compile.

✅ **Pros:**

* ⏱ Shorter run-time on large designs
* 📝 Module-by-module debug and reporting
* 🤝 Easier integration with verification flows

⚠️ **Cons:**

* 🔒 Limited cross-module optimization
* 🧰 Sometimes requires extra setup for accurate timing across modules

📷 **Screenshot 2: Hierarchical Synthesis Output**
![🗂 Hierarchical Synthesis Output](images/hierarchical_synth.png)

### 🛠 Flattened Synthesis

* 📌 **Meaning:** All modules merged into a single netlist.
* ⚙️ **How Tools Handle It:** `flatten` in Yosys removes hierarchy boundaries.
* 🎯 **When Useful:** When you need maximum area/timing optimization.

✅ **Pros:**

* 🌐 Full-design optimization (timing, area)
* 📄 Single netlist may simplify back-end

⚠️ **Cons:**

* 🐢 Can run slower on big designs
* 🧐 Debugging is harder because you lose the RTL structure
* 💾 Larger memory footprint

📷 **Screenshot 3: Flattened Synthesis Output**
![🛠 Flattened Synthesis Output](images/flattened_synth.png)

| 📝 Feature            | 🗂 Hierarchical | 🛠 Flattened  |
| --------------------- | --------------- | ------------- |
| 🏗 Structure kept?    | ✅ Yes           | ❌ No          |
| 🎯 Optimization scope | 🔹 Per module   | 🔸 Whole chip |
| ⏱ Run-time            | ⚡ Faster        | 🐢 Slower     |
| 🧐 Debug visibility   | 👀 Clear        | 😵 Reduced    |

---

## 3️⃣ 💾 Flip-Flop RTL Templates for Clean Synthesis

Flip-flops store binary data on a clock edge ⏰. Writing them correctly ensures the tool maps them to standard cell DFFs with the right reset/set behavior.

### ⏱ Asynchronous Reset DFF

```verilog
module dff_async_reset (input clk, input rst, input d, output reg q);
  always @(posedge clk or posedge rst)
    if (rst) q <= 1'b0;
    else     q <= d;
endmodule
```

* 🔄 **Reset immediately overrides clock**.

### ⏱ Asynchronous Set DFF

```verilog
module dff_async_set (input clk, input set, input d, output reg q);
  always @(posedge clk or posedge set)
    if (set) q <= 1'b1;
    else     q <= d;
endmodule
```

* 🔄 **Set immediately overrides clock**.

### ⏱ Synchronous Reset DFF

```verilog
module dff_sync_reset (input clk, input rst, input d, output reg q);
  always @(posedge clk)
    if (rst) q <= 1'b0;
    else     q <= d;
endmodule
```

* ⏰ **Reset only happens on clock edge**.

---

## 4️⃣ 🧰 Simulation & Synthesis Flow – Understanding the Tools

### 🖥 Icarus Verilog (Simulator)

* 🛠 Compiles and runs your Verilog test benches.
* 📄 Produces a Value Change Dump (.vcd) for waveform viewing.

Run:

```bash
iverilog dff_async_reset.v tb_dff_async_reset.v
./a.out
```

### 📈 GTKWave (Waveform Viewer)

* 👀 Opens `.vcd` files to see signals over time.

```bash
gtkwave tb_dff_async_reset.vcd
```

📷 **Screenshot 4: GTKWave Waveform Screenshot**
![📈 GTKWave Waveform Screenshot](images/gtkwave_waveform.png)

### 🧠 Yosys (Synthesizer)

* 📥 Reads RTL and Liberty libraries
* 🔧 Generates a gate-level netlist mapped to standard cells
* 📝 Commands you’ll use:

```tcl
yosys
read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_async_reset.v
synth -top dff_async_reset
dfflibmap -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

* 🔄 `dfflibmap` chooses flip-flop cells
* 🧮 `abc` does technology mapping & optimization
* 🖥 `show` opens an interactive schematic of the mapped netlist

---

## ✅🎓 Key Takeaways from Day 2

* 🕒 **Timing libraries** supply cell delay/power info for synthesis
* 🔀 **Hierarchical vs. flat synthesis** affects runtime, optimization, and debug
* 💾 **Flip-flop coding style** determines how your RTL maps to real cells
* 🧰 **Tools in the flow**:

  * 🖥 *Icarus Verilog* = simulate
  * 📈 *GTKWave* = view signals
  * 🧠 *Yosys* = synthesize and map to SKY130
