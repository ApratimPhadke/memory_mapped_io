---

# 🖥️ Simple Memory-Mapped I/O in Verilog

A beginner-friendly but professional project that demonstrates how **Memory-Mapped I/O (MMIO)** works using Verilog HDL.
This design simulates how processors communicate with **peripherals (LEDs, Switches, UART, etc.)** using memory-mapped registers.

---

## 📌 Project Overview

In real processors, I/O devices are mapped into the memory space. The CPU can **read/write peripherals just like memory** using addresses.

👉 This project implements a **small MMIO system** with:

* **CPU Bus Interface** → Address, Data, Read/Write controls
* **Registers for I/O devices**
* **LED and Switch (as example peripherals)**
* **Testbench to validate read/write operations**

---

## ⚡ Features

* ✅ Implements **4 memory-mapped registers**

  * `0x00` → Input Switch
  * `0x04` → Output LED
  * `0x08` → Status Register
  * `0x0C` → Control Register
* ✅ Simple, fully **synthesizable Verilog** design
* ✅ Includes **Testbench** for simulation
* ✅ Modular design → easy to extend for UART, GPIO, Timers, etc.
* ✅ Helps understand **CPU ↔ Peripheral communication**

---

## 🏗️ System Architecture

Here’s the high-level block diagram of our design:

<img width="2048" height="2048" alt="Gemini_Generated_Image_a9d1oua9d1oua9d1" src="https://github.com/user-attachments/assets/2e765d12-bea1-418d-ac2b-7bb335d0f5d2" />
<img width="1854" height="1168" alt="Screenshot from 2025-08-20 15-08-39" src="https://github.com/user-attachments/assets/4e7f0bb1-b3a5-49f5-954c-ccd0473ad329" />
<img width="1854" height="1168" alt="Screenshot from 2025-08-20 15-11-50" src="https://github.com/user-attachments/assets/2853cba2-29bb-4a25-a56b-e6580db0bfd7" />


---

---

## 🔑 Verilog Code

### MMIO Module (`memory_mapped_io.v`)

```verilog
`timescale 1ns/1ps
`default_nettype none

module memory_mapped_io (
    input  wire        clk,
    input  wire        reset,
    input  wire [7:0]  addr,
    input  wire [7:0]  wdata,
    input  wire        we,       // Write Enable
    input  wire        re,       // Read Enable
    output reg  [7:0]  rdata,
    input  wire [7:0]  switch_in,
    output reg  [7:0]  led_out
);

    reg [7:0] status_reg;
    reg [7:0] control_reg;

    always @(posedge clk or posedge reset) begin
        if (reset) begin
            led_out     <= 8'b0;
            status_reg  <= 8'b0;
            control_reg <= 8'b0;
        end else if (we) begin
            case (addr)
                8'h04: led_out     <= wdata;
                8'h08: status_reg  <= wdata;
                8'h0C: control_reg <= wdata;
                default: ; // Do nothing
            endcase
        end
    end

    always @(*) begin
        if (re) begin
            case (addr)
                8'h00: rdata = switch_in;
                8'h04: rdata = led_out;
                8'h08: rdata = status_reg;
                8'h0C: rdata = control_reg;
                default: rdata = 8'h00;
            endcase
        end else begin
            rdata = 8'h00;
        end
    end

endmodule
```

---

### Testbench (`tb_memory_mapped_io.v`)

```verilog
`timescale 1ns/1ps
`default_nettype none

module tb_memory_mapped_io;
    reg clk, reset, we, re;
    reg [7:0] addr, wdata, switch_in;
    wire [7:0] rdata, led_out;

    memory_mapped_io dut (
        .clk(clk),
        .reset(reset),
        .addr(addr),
        .wdata(wdata),
        .we(we),
        .re(re),
        .rdata(rdata),
        .switch_in(switch_in),
        .led_out(led_out)
    );

    initial begin
        clk = 0; forever #5 clk = ~clk;
    end

    initial begin
        reset = 1; we = 0; re = 0; addr = 0; wdata = 0; switch_in = 8'hA5;
        #12 reset = 0;

        // Write LED register
        #10 addr = 8'h04; wdata = 8'hF0; we = 1;
        #10 we = 0;

        // Read LED register
        #10 addr = 8'h04; re = 1;
        #10 re = 0;

        // Read Switch input
        #10 addr = 8'h00; re = 1;
        #10 re = 0;

        #20 $stop;
    end
endmodule
```

---

## ▶️ Simulation

Run the testbench in **ModelSim/iverilog**:

```sh
iverilog -o mmio_tb tb_memory_mapped_io.v memory_mapped_io.v
vvp mmio_tb
```

Expected Output (example):

* LED register written with `0xF0`
* Switch register read `0xA5`

---

## 📸 Screenshots

* Simulation Waveforms
* Register Write/Read Results

<img width="1387" height="894" alt="Screenshot from 2025-08-20 15-23-11" src="https://github.com/user-attachments/assets/5d69b997-540c-41d0-95e9-b2c077c6c68f" />
<img width="1387" height="894" alt="Screenshot from 2025-08-20 15-11-22" src="https://github.com/user-attachments/assets/34e711a9-fe14-4d05-99b2-bc4e88991d2b" />
<img width="1854" height="1168" alt="Screenshot from 2025-08-20 15-26-16" src="https://github.com/user-attachments/assets/ea1d8de6-0192-4fda-bf3e-92a10454b689" />
<img width="1854" height="1168" alt="Screenshot from 2025-08-20 15-26-28" src="https://github.com/user-attachments/assets/4ddb4510-647a-4774-96fb-a1ecef087125" />




---

## 🚀 Future Work

* Add **UART mapped register**
* Add **Timer/Counter peripheral**
* Expand memory map to **16 registers**

---

## 📜 License

This project is released under the **MIT License**.

---

✨ With this project, you’ll clearly understand how **CPU ↔ Peripheral communication via Memory-Mapped I/O** works in hardware design!

---

