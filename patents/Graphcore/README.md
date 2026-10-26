# Graphcore

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred. Assignee list: [Justia, Graphcore Limited](https://patents.justia.com/assignee/graphcore-limited).

---

## 1. Why Graphcore matters for your kernel

Graphcore's IPU **deliberately publishes no tensor layout formula** — layout is an output of the Poplar graph compiler's convolution planner, not a fixed format (the only public number is that the planner tries channel groups of 16, 8 or 4; option `experimental.slicVmac16`). So Graphcore's patents are about **the compiler side**: generating **layout-conversion (data transfer) instructions** from an analysis of what the accelerator will execute (F3), compiling a program from a graph (F1), and the instruction set (F1/F2). This is the "keep layout a planner decision, own the whole stack" fork noted in your vendor file's conclusion.

---

## 2. Summary table

| # | Number | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US11449254B2 | Managed bulk memory | **F3** | granted 20 Sep 2022 | V |
| 2 | US11720332B2 | Compiling a program from a graph | F1 | granted | V |
| 3 | US12141092B2 | Instruction set | F1 + F2 | granted | V |

---

## 3. Patent details

### 3.1 US11449254B2 — Managed bulk memory  (layout conversion)
- **Assignee:** Graphcore Limited. **Inventors:** Richard Osborne, Chad Jarvis, Fabian Tschopp, Tim Hutt, Emmanuel Menage. Filed 17 Sep 2020; granted 20 Sep 2022.
- **What it covers.** **Data transfer instructions for converting a tensor between two layouts**: a first layout for host memory and a second for the external memory the accelerator subsystem reads; the **conversion instructions are generated from an analysis of the instructions the subsystem will execute**.
- **In three-formula terms (analysis).** F3 as a derived artifact: the compiler knows the subsystem's schedule (F1) and accesses (F2), and emits the host->device re-layout to match — the generalised "reorder only when necessary" pass, the same idea as oneDNN Graph layout propagation.
- **Links:** [Justia 11449254](https://patents.justia.com/patent/11449254) · [Justia, Tim Hutt](https://patents.justia.com/inventor/tim-hutt)

### 3.2 US11720332B2 — Compiling a program from a graph
- Compile a Poplar-style compute graph (tensors mapped to tile memory, vertices mapped to tiles) into tile programs. F1. **Link:** [Google Patents US11720332](https://patents.google.com/patent/US11720332)

### 3.3 US12141092B2 — Instruction set
- The IPU instruction set (includes addressing / data-access instructions). F1 + F2. **Link:** [Google Patents US12141092](https://patents.google.com/patent/US12141092)

---

## 4. Public (non-patent) material
- Convolution planner tries channel groups of 16, 8 or 4; `experimental.slicVmac16` forces grouping of 16: [Poplar Convolution.hpp](https://docs.graphcore.ai/projects/poplar-api/en/latest/doxygen/Convolution_8hpp.html)
- GC200: 1,472 tiles, 6 threads each, 624 KB local SRAM per tile (third-party).

## 5. Searched and not found
- No Graphcore patent found that fixes a tensor layout formula or a convolution access map — by design, these are planner outputs.

## 6. Search log
- `Graphcore patent IPU convolution tensor memory layout "channels per group" OR "tile" compiler planner poplar patent assignee`
- `Graphcore Limited patent Tim Hutt US11467834 OR layout "data transfer instructions" conversion between two layouts subsystem claims`
