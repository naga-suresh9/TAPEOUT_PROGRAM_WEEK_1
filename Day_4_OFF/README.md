# 🚀 Day 4: Gate-Level Simulation, Verilog Assignments & Synthesis-Simulation Harmony 🎯

Welcome to **Day 4** of the RTL Workshop! Today, we unlock three 🔑 pillars of digital design verification:

* 🔍 **Gate-Level Simulation (GLS)**
* ⚖️ **Blocking vs. Non-Blocking Assignments in Verilog**
* 🔄 **Synthesis-Simulation Mismatch: Causes & Fixes**

Let’s get into theory, practical tips, and hands-on labs! 🛠️💡

---

## 📚 Table of Contents

1. ⚡ Gate-Level Simulation (GLS)
2. ⚠️ Synthesis-Simulation Mismatch
3. 🧩 Blocking vs. Non-Blocking Assignments

   * 3.1 Blocking Assignments (`=`)
   * 3.2 Non-Blocking Assignments (`<=`)
   * 3.3 Side-by-Side Comparison
4. 🛠️ Labs & Examples
5. 📌 Summary & Pro Tips
6. 💡 Extra Insights & Best Practices

---

## 1. ⚡ Gate-Level Simulation (GLS)

GLS is the **post-synthesis verification step** that simulates the actual gate-level netlist to ensure your design behaves correctly in silicon.

### Why GLS? 🤔

* ✅ **Synthesis Validation:** Confirms RTL correctly converted to gates.
* ⏳ **Timing Verification:** Uses SDF delay files to catch timing violations (setup/hold).
* 🧪 **Testability Check:** Validates scan chains and DFT structures.

### When to Perform? ⏰

* Right after synthesis, **before layout**, to catch issues early and save debugging time.

### Types of GLS 🛠️

* **Functional GLS:** Logic-only, zero or minimal delays.
* **Timing GLS:** Realistic timing simulation using annotated delay files.

> 💡 *Pro Tip:* Always include **SDF files** for accurate timing checks!

---

## 2. ⚠️ Synthesis-Simulation Mismatch

Sometimes, RTL sim results differ from GLS or hardware. Here’s why:

### Common Causes 🔍

* Using **non-synthesizable constructs** like `initial` blocks, `#` delays.
* Incomplete sensitivity lists (e.g., missing inputs in combinational blocks).
* Ambiguous or incomplete RTL coding (missing `else`, latch inference).
* Differences in how synthesis vs. simulation tools interpret code.

### How to Avoid 🚫

* Write **clean, synthesizable RTL** only.
* Use `always @(*)` for combinational logic to cover all inputs.
* Keep testbench-only constructs (delays, resets) out of RTL.

---

## 3. 🧩 Blocking vs. Non-Blocking Assignments in Verilog

### 3.1 Blocking Assignments (`=`)

* Execute **immediately, sequentially**.
* Ideal for **combinational logic** (`always @(*)`).
* Example:

  ```verilog
  always @(*) y = a & b;
  ```

### 3.2 Non-Blocking Assignments (`<=`)

* Scheduled for **end of time step**, all update simultaneously.
* Perfect for **sequential logic** (`always @(posedge clk)`).
* Example:

  ```verilog
  always @(posedge clk) q <= d;
  ```

### 3.3 Quick Comparison Table 📊

| Feature              | Blocking (`=`)           | Non-Blocking (`<=`)           |
| -------------------- | ------------------------ | ----------------------------- |
| Execution            | Immediate, in code order | Scheduled, concurrent         |
| Use Case             | Combinational logic      | Sequential logic (flip-flops) |
| Update Timing        | Instantly updates        | Updates after current step    |
| Synthesized Hardware | Logic gates              | Flip-flops/registers          |

> ⚠️ **Mixing blocking and non-blocking in same block leads to bugs!** Keep them separated by logic type.

---

## 4. 🛠️ Labs & Examples

### Lab 1: Simple 2:1 MUX with Ternary Operator

```verilog
module ternary_operator_mux(input i0, input i1, input sel, output y);
  assign y = sel ? i1 : i0;
endmodule
```

* Output `y` = `i1` if `sel`=1, else `i0`.
<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/29cee5fa-a149-4b93-b64e-878a936c6612" />
---

### Lab 2: Synthesize MUX with Yosys

* Run standard synthesis flow to generate gate-level netlist.
<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/95b8bedc-eafd-4b75-9e0f-10bc74f9f06d" />
---

### Lab 3: Gate-Level Simulation of MUX

* Use Icarus Verilog + cell libraries + testbench for GLS.
<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/db1eea65-dd58-4b69-b5a6-11511649c612" />
---

### Lab 4: Bad MUX — Common Mistakes 🚩

```verilog
module bad_mux(input i0, input i1, input sel, output reg y);
  always @(sel) begin
    if (sel)
      y <= i1;
    else
      y <= i0;
  end
endmodule
```

* Issues:

  * Incomplete sensitivity list (`i0` & `i1` missing).
  * Using non-blocking (`<=`) in combinational logic (not ideal).

**Fixed Version:**

```verilog
always @(*) begin
  if (sel)
    y = i1;
  else
    y = i0;
end
```

---

### Lab 5: GLS on Bad MUX

* Expect warnings & mismatches. Great debugging practice!

---

### Lab 6: Blocking Assignment Order Caveat ⚠️

```verilog
module blocking_caveat(input a, input b, input c, output reg d);
  reg x;
  always @(*) begin
    d = x & c;
    x = a | b;
  end
endmodule
```

* Problem: `d` uses old value of `x`.

**Corrected Order:**

```verilog
always @(*) begin
  x = a | b;
  d = x & c;
end
```

---

### Lab 7: Synthesis of Corrected Module

* Synthesize and verify expected results.

---

## 5. 📌 Summary & Pro Tips

* **GLS** is essential to validate synthesis, timing, and testability.
* Avoid **synthesis-simulation mismatches** with clean, synthesizable RTL.
* Use **blocking (`=`)** for combinational, **non-blocking (`<=`)** for sequential logic.
* Always simulate **both RTL and gate-level netlists**!
* Carefully review all **tool warnings**—they save your design from costly mistakes.

---

## 6. 💡 Extra Insights & Best Practices

### ⏱️ Timing Annotation & SDF Files

Accurate delay info in SDF files is crucial for realistic GLS timing checks and catching violations early.

### 🔄 Sensitivity List Best Practice

Use `always @(*)` to automatically include all inputs in combinational logic blocks—no guesswork.

### 🤝 Sequential Logic & Non-Blocking Assignments

Non-blocking assignments reflect real flip-flop behavior where all registers update simultaneously, preventing race conditions.

### 🐞 Debugging GLS

GLS is slower and can be tricky—use waveform viewers and signal mapping files to correlate gate-level signals back to RTL.

---

## 📖 Glossary of Key Terms

| Term                     | Meaning                                          |
| ------------------------ | ------------------------------------------------ |
| **SDF**                  | Standard Delay Format — timing annotations       |
| **Netlist**              | Gate-level representation of RTL after synthesis |
| **Scan Chains**          | Test structures for Design-for-Test (DFT)        |
| **Setup/Hold Violation** | Timing error where data arrives too early/late   |

---

✨ **Final Pro Tip:** Simulate early and often, from RTL to GLS, and don’t ignore warnings! It will save you countless debugging hours. 🚀👩‍💻👨‍💻
