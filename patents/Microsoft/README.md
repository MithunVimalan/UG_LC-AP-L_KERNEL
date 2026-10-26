# Microsoft (Maia, Project Brainwave)

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred. Assignee: Microsoft Technology Licensing, LLC.

---

## 1. Why Microsoft matters for your kernel

Microsoft's AI hardware (FPGA Project Brainwave; Maia 100 ASIC) and its compiler stack cover all three formulas:
- **F1** — "Compiling tensor operators ... based on tensor tile configurations" chosen from the target hardware's specs (US12572341) — tile-size selection, your 1x3-vs-2x4 decision, as a compiler patent.
- **F2 + F3** — "Convolutional neural networks on hardware accelerators" and a **block-floating-point convolution** accelerator (inventors Kalin Ovtcharov, Eric Chung).

Maia 100 uses L1/L2 scratchpads and MX (microscaling) low-precision formats — the block-floating-point line below.

---

## 2. Summary table

| # | Number | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US12572341 | Compiling tensor operators for neural network models (tensor tile configurations) | **F1** | granted | V |
| 2 | US20160379109A1 | Convolutional neural networks on hardware accelerators | F2 | application | V |
| 3 | (grant, filed 18 Mar 2019, issued 17 Oct 2023) | Hardware accelerator performing convolution with block floating-point format | F2 + F3 | granted | V |

## 3. Patent details

### 3.1 US12572341 — Compiling tensor operators based on tensor tile configurations
- **Assignee:** Microsoft Technology Licensing, LLC. "Compiling tensor operators for neural network models based on **tensor tile configurations**", the configuration chosen from the **specifications of a set of hardware devices**.
- **In three-formula terms (analysis).** F1: pick the tile shape (your output tile) from the hardware (register count, cache, vector width) — the automated form of your section 9.1 reasoning.
- **Link:** [Justia, Microsoft Technology Licensing](https://patents.justia.com/assignee/microsoft-technology-licensing-llc?page=3)

### 3.2 US20160379109A1 — Convolutional neural networks on hardware accelerators
- Convolution mapped onto an FPGA/accelerator (Brainwave lineage). F2. [Google Patents citation](https://patents.google.com/patent/US20160379109A1/en)

### 3.3 Block-floating-point convolution accelerator
- **Inventors:** Kalin Ovtcharov, Eric S. Chung. A hardware accelerator that performs **convolution of input and kernel data using a block floating-point format** (shared exponent per block) — the format Maia's MX support descends from. F2 (conv) + F3 (block-FP layout). Filed 18 Mar 2019, issued 17 Oct 2023.

## 4. Public (non-patent) material
- Project Brainwave: FPGA NPU with matrix-vector multipliers, attached to the datacenter network: [Microsoft Research](https://www.microsoft.com/en-us/research/news-and-awards/)
- Maia 100: L1/L2 scratchpads, MX low-precision formats: [glennklockwood.com Maia-100](https://glennklockwood.com/garden/processors/Maia-100)

## 5. Search log
- `Microsoft patent neural network accelerator "Maia" OR Brainwave convolution data layout tensor tiling FPGA systolic matrix US assignee`
- `"Microsoft Technology Licensing" patent neural network "data layout" OR tensor convolution accelerator matrix tile block justia`
