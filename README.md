# 32-BIT CPU

A custom-designed **32-bit CPU built from transistors and ICs** — designed, simulated and
documented from scratch as part of [Hack Club Fallen](https://fallen.hackclub.com/).

> **Project page:** [fallen.hackclub.com/projects/prj_k6lFJr](https://fallen.hackclub.com/projects/prj_k6lFJr)

---

## About

This is a ground-up CPU project: no microcontroller, no FPGA — just discrete logic,
timers and latches wired together until they add up to a processor that can execute
instructions.

The approach is deliberately bottom-up. Before a CPU can compute anything it needs a
**clock**, and before it can store anything it needs **memory**, so the project started
at the two smallest possible ends: a 555-based clock generator and a single bit of
storage.

Everything is being drawn properly in KiCad with the end goal of producing a complete
PCB for the finished CPU.

## Repository layout

| Folder | Contents |
| --- | --- |
| `KiCad Files/555-Timer/` | Clock generator schematics — astable, monostable and bistable modes, mode-select mux and the sheet hierarchy (`Clock.kicad_sch`, `Bistable.kicad_sch`), plus the PCB layout |
| `KiCad Files/Latches/` | SR / D latch schematic and PCB — the 1-bit storage element of the CPU |
| `Logisim/` | Simulated latch circuits (`Latch.circ`, `D_Latch_Enable.circ`) |
| `Spice Files/` | LTspice simulation of the 555 timer (`Draft1.asc` + sim output) |
| `Schematic PDF/` | Exported schematic PDFs for quick viewing without KiCad |

## Build log

The project is documented session-by-session in journals on the project page. A
condensed version:

### 1 Nov — Day 1: the clock
Started with timers, because a CPU is nothing without a clock. Researched the
**555 timer** and its modes (astable / monostable), then drew a schematic in KiCad and
modelled it in LTspice.

*Problems faced:*
- Still learning how to drive the Spice simulator inside KiCad.
- On the breadboard, a calculation mistake **fried a resistor**.
- A capacitor labelled **1 µF was actually 2 µF** — an expected 180 ms timing came out
  at 270 ms. Swapped it out and the maths held up again.

### 2 Nov — Manual stepping & debouncing
Added a push-button so the future CPU can be clocked **one step at a time** during
debugging.

*Problem:* a raw push-button bounces, closing the circuit several times per press and
advancing the CPU more than one clock — fatal when debugging.

*Solution:* pull-up resistor plus the **SR latch already inside the 555** to debounce —
the latch holds until the capacitor discharges to 3.3 V. The discharge resistor between
pins 6 and 7 was removed so the capacitor drops out instantly.

### 3 Nov — Finishing the clock
Added a **DPDT switch** to move between the 555's modes. That switch is
break-before-make, so it needed debouncing too — again handled by the 555's SR latch.

The modes are combined with a **2-input mux (74HC157)** (equivalently, some gates),
and global labels carry signals between schematic sheets. This produced the complete
clock circuit: astable + monostable + bistable + manual step, all in one.

*10+ hours logged on this one.*

### 4 Nov — Latches (storage)
Moved on to storing a bit:

1. First attempt: a single **OR gate with its output fed back** — the LED never
   latched. Turned out **Crumb's simulator doesn't support feedback**, so it was a tool
   limitation, not a design flaw.
2. Rebuilt in **Logisim**, where it worked immediately. Also built an **SR latch** and
   a **D latch with enable**.
3. Drew the latch schematic in KiCad (the goal: a proper PCB at the end).
4. Connected clock to latch: an **RC differentiator** converts the clock's high pulse
   into the short **enable** pulse the D latch needs.

### Parallel — the software side
While the hardware came together, work started on the other half of a CPU: **assembly**
and instruction handling, plus study of **ALU architecture** and digital logic
(AND / OR / NAND / NOR / NOT behaviour), leading toward **carry-lookahead adders** and
how a CPU actually fetches and reads instructions.

## Status

**Done**
- [x] 555 timer: astable, monostable and bistable modes simulated and breadboarded
- [x] Complete clock: mode mux, manual single-step, debounced push-button
- [x] SR latch, D latch with enable — simulated in Logisim, schematic in KiCad
- [x] Clock → enable pulse conversion (RC differentiator)

**Next**
- [ ] ALU — starting with the adder (carry-lookahead adder)
- [ ] Instruction fetch / decode — how the CPU reads instructions
- [ ] Register file and data path
- [ ] Assembly toolchain for the software side
- [ ] Full 32-bit CPU PCB

## Tools

- **KiCad** — schematics and PCB
- **LTspice** — analogue simulation of the 555 timer
- **Logisim** — digital logic simulation (latches)
- **Crumb** — early experiments (does not support feedback loops, so dropped for
  anything sequential)

## Team

| | Role | Focus |
| --- | --- | --- |
| **Sahil Virdi** | Owner | Hardware — clocks, latches, schematics, PCB |
| **akshnoorbawa2** | Member | Software — assembly, ALU and CPU architecture study |

**47h 30m logged across 7 journals.**

---

*This project is being built for Hack Club Fallen — designed, simulated and documented
in the open, mistakes and fried resistors included.*
