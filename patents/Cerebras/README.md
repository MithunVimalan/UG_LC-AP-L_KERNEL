# Cerebras Systems

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred.

---

## 1. Why Cerebras matters for your kernel

Cerebras's WSE is a wafer-scale grid of ~900k PEs, each with a core, 48 KB local SRAM and a router. Tensors are first-class via **Data Structure Registers (DSRs)** that carry tensor metadata, and hardware state machines traverse tensors in several **layout modes** — which is F2 + F3 in hardware. The public record found here is architectural (Hot Chips decks, blog), **not** patents: no Cerebras patent on data layout, access map or scheduling surfaced in these searches. Treat Cerebras as **not public at the patent level** for the three formulas until a dedicated assignee search is run.

---

## 2. Status

| Formula | What is public | Patent found? |
|---|---|---|
| F1 schedule | "Place & Route allocates compute and memory, configures on-chip network" (Hot Chips 2020); dataflow for sparse/dynamic workloads | not found in these searches |
| F2 access map | DSRs + hardware state machines traverse tensors in several layout modes (secondary source) | not found |
| F3 layout | per-PE 48 KB SRAM; tensor metadata in 44 DSRs | not found |

## 3. Public (non-patent) sources
- [Hot Chips 2020, Cerebras ML training deck](https://hc32.hotchips.org/assets/program/conference/day2/HotChips2020_ML_Training_Cerebras_v03.pdf) — Place & Route, dataflow for sparse/dynamic workloads
- [Hot Chips 2019, Sean Lie](https://old.hotchips.org/hc31/HC31_1.13_Cerebras.SeanLie.v02.pdf)
- [Hot Chips 2022, Cerebras](https://hc34.hotchips.org/assets/program/conference/day2/Machine%20Learning/HC2022_Cerebras_Final_v02.pdf)
- WSE-2 Selectable Sparsity: [Cerebras blog](https://www.cerebras.ai/blog/an-ai-chip-with-unprecedented-performance-to-do-the-unimaginable)
- Sparse matmul on CS-3: [arXiv 2604.27985](https://arxiv.org/abs/2604.27985)

## 4. Searched and not found
- No Cerebras patent on tensor data layout, convolution access map, or scheduling surfaced. A follow-up assignee search ("Cerebras Systems") and inventor search (e.g. Sean Lie) is the next step; this folder is a stub pending that.

## 5. Search log
- `Cerebras Systems patent wafer scale tensor "data layout" OR "weight" OR "scheduling" sparse convolution dataflow fabric patent`
