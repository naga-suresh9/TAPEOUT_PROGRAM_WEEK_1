# 📝🌟 Day 5 — Optimization in Synthesis (RTL Workshop)

> 🎯 **Goal:** Learn how coding style affects synthesis results, avoid inferred latches, and practice loops & generate blocks.

---

## 📂✨ Topics Covered

| #️⃣ | 📑 Topic                                         |
| --- | --------------------------------------------- |
| 1️⃣ | ⚡ Conditional Coding: `if`–`else` & Nested `if` |
| 2️⃣ | 🛑 Inferred Latches & How to Avoid Them          |
| 3️⃣ | 🧪 Labs on `if`/`case` Statements                |
| 4️⃣ | 🔁 For Loops in Verilog                          |
| 5️⃣ | 🏗️ Generate Blocks in Verilog                    |
| 6️⃣ | ➕ Ripple Carry Adder (RCA) Overview             |
| 7️⃣ | 🧩 Labs on Loops & Generate Blocks               |
| 8️⃣ | 📝 Key Take-aways                                |

---

## 1️⃣ ⚡ Conditional Coding — `if` / `else`

* 📝 Used inside **procedural blocks**: `always`, `initial`, tasks, functions.
* 🖋️ General form:

  ```verilog
  if (condition) begin
      // statements when true
  end else begin
      // statements when false
  end
````

* 🔀 **Nested If-Else**:

  ```verilog
  if (cond1)
      // ...
  else if (cond2)
      // ...
  else
      // ...
  ```

---

## 2️⃣ 🛑 Inferred Latches

⚠️ When a combinational `always` block doesn’t assign outputs for **all paths**, synthesis infers a latch.

**🚫 Example – Latch inferred:**

```verilog
always @(a,b,sel) begin
    if (sel)
        y = a;   // no assignment when sel==0 → latch
end
```

**✅ Fixed with default/else:**

```verilog
always @(a,b,sel) begin
    case(sel)
        1'b1 : y = a;
        default: y = 1'b0;
    endcase
end
```

📷 <img width="1920" height="1080" alt="in_complete_if" src="https://github.com/user-attachments/assets/758b4747-2e9d-4645-9831-2df920e84d52" />


---

## 3️⃣ 🧪 Hands-On Labs: `if` / `case`

> 📝 **Follow the Day 1 steps** to simulate and synthesize each lab. Insert screenshots below each heading.

* 🔬 **Lab 1 – Incomplete If**

  ```verilog
  module incomp_if (input i0,i1,i2, output reg y);
      always @(*) begin
          if (i0)
              y <= i1;
      end
  endmodule
  ```

  📷 <img width="1920" height="1080" alt="in_comp_if_schematic" src="https://github.com/user-attachments/assets/8e999f4a-e99d-4bb9-afa6-41846c0bc8d1" />


* 🔬 **Lab 3 – Nested If-Else**

  ```verilog
  module incomp_if2 (input i0,i1,i2,i3, output reg y);
      always @(*) begin
          if (i0)
              y <= i1;
          else if (i2)
              y <= i3;
      end
  endmodule
  ```

  📷 <img width="1920" height="1080" alt="tb_incomp_if2" src="https://github.com/user-attachments/assets/24d8d295-ab54-4c4e-8eca-f859246eb5e5" />


* 🔬 **Lab 5 – Complete Case**

  ```verilog
  module comp_case (input i0,i1,i2,
                    input [1:0] sel,
                    output reg y);
      always @(*) begin
          case(sel)
              2'b00: y = i0;
              2'b01: y = i1;
              default: y = i2;
          endcase
      end
  endmodule
  ```

  📷 <img width="1920" height="1080" alt="comp_case" src="https://github.com/user-attachments/assets/96d0b6a2-06d0-401a-8a58-1f1d104d43d5" />
<img width="1920" height="1080" alt="comp_case_schematic" src="https://github.com/user-attachments/assets/43350631-fc6d-4ad4-a9c4-f7c9763d6818" />


* ⚠️ **Lab 7 – Incomplete Case Handling**
  (Wildcard example – be careful!)
<img width="1920" height="1080" alt="incomp_case_schematic" src="https://github.com/user-attachments/assets/ee510a87-7f72-4732-88ed-e74dbcea7654" />


* 📝 **Lab 8 – Partial Assignments**
  (Multiple outputs in one case branch.)

📷  <img width="1920" height="1080" alt="partial_case_assign" src="https://github.com/user-attachments/assets/aaad7f39-d5db-4971-bdc2-a92a8ca8192d" />



## 4️⃣ 🔁 For Loops in Verilog

* 🏷️ Only valid **inside procedural blocks**.
* ⏳ **Iteration count must be fixed at compile time** for synthesis.

**Example – 4-to-1 MUX using for loop:**

```verilog
integer i;
always @(data,sel) begin
    y = 1'b0;
    for (i=0;i<4;i=i+1)
        if (i==sel)
            y = data[i];
end
```

📷 <img width="1920" height="1080" alt="mux_generate_gtkwave" src="https://github.com/user-attachments/assets/28810018-8f68-47fe-bbbd-3bea3d65a094" />


---

## 5️⃣ 🏗️ Generate Blocks

* 🏭 Used to create **multiple instances** at compile time.
* 🔑 Combine with `genvar` and `for`.

**Example – AND array:**

```verilog
genvar i;
generate
  for (i=0;i<4;i=i+1) begin: gen_loop
    and_gate and_inst (.a(in[i]), .b(in[i+1]), .y(out[i]));
  end
endgenerate
```

📷 <img width="1920" height="1080" alt="demux_generate_gtkwave" src="https://github.com/user-attachments/assets/2fd340bf-95a5-4e5e-a5ae-80dcf101937c" />


---

## 6️⃣ ➕ Ripple Carry Adder (RCA)

* ➡️ Adds n-bit numbers using a chain of **full adders**.
* 🔗 Carry out of each stage feeds next stage’s carry in.

📷 <img width="1920" height="1080" alt="rca_gtkwave" src="https://github.com/user-attachments/assets/37026128-a953-42ff-b58e-c34340971f1c" />


---

## 7️⃣ 🧩 Labs: Loops & Generate Blocks

| 🧮 Lab | 📝 Description                 |
| ------ | ------------------------------ |
| 9️⃣    | 4-to-1 MUX using For Loop      |
| 🔟     | 8-to-1 Demux using Case        |
| 1️⃣1️⃣ | 8-to-1 Demux using For Loop    |
| 1️⃣2️⃣ | 8-bit RCA using Generate Block |

*(Full Verilog code given in your original notes.)*

📷 <img width="1920" height="1080" alt="demux_case_gtkwave" src="https://github.com/user-attachments/assets/3315b766-db38-429d-8308-6ae8961596c9" />
<img width="1920" height="1080" alt="demux_generate_gtkwave" src="https://github.com/user-attachments/assets/9705c2c4-0fe6-4972-9ec1-d6f99b192f52" />


---

## 8️⃣ 📝 Summary & Key Points

* ✅ Always **assign outputs for all paths** to avoid unintended latches.
* ✅ Use **complete case/if statements** with default values.
* ✅ For loops & generate blocks = **clean, scalable code**.
* ✅ Hands-on labs reinforce theory.

