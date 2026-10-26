# Meta (Meta Platforms — MTIA)

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred.

---

## 1. Why Meta matters for your kernel

Meta's MTIA inference/training accelerator is an 8x8 grid of PEs (RISC-V control + vector engines, 128 KB local memory each, 256 MB shared L2). Its patents cover the **accelerator architecture** and an **efficient memory layout for data compression** — F3 — plus a hardware/ISA for ML compute (F1).

---

## 2. Summary table

| # | Number | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US20190325303A1 | Machine learning accelerator architecture | F1 (+F3) | application | V (Meta Platforms, Inc.) |
| 2 | US20210004668A1 | Neural network accelerator | F1 | application | V (Meta Platforms Technologies, LLC) |

## 3. Patent details

### 3.1 US20190325303A1 — Machine learning accelerator architecture
- **Assignee:** Meta Platforms, Inc. Accelerator circuitry with **separate PE sets for matrix multiplication and for weight/input offset sums**, running in parallel; the family also has a related title on an **efficient memory layout for data compression** in ML.
- **In three-formula terms (analysis).** F1: splitting the schedule so the quantization offset sums run alongside the matmul. F3: the compression-oriented memory layout.
- **Link:** [Google Patents US20190325303A1](https://patents.google.com/patent/US20190325303A1/en)

### 3.2 US20210004668A1 — Neural network accelerator
- **Assignee:** Meta Platforms Technologies, LLC. Hardware + instruction-set architecture for ML computations. F1. [Google Patents US20210004668A1](https://patents.google.com/patent/US20210004668A1/en)

## 4. Public (non-patent) material
- MTIA v2: 8x8 PE grid, RISC-V + vector engines, 128 KB local memory/PE, 256 MB shared L2, LPDDR5: [NextPlatform](https://www.nextplatform.com/2024/04/10/with-mtia-v2-chip-meta-can-do-ai-training-as-well-as-inference)

## 5. Searched and not found
- No Meta patent found that fixes an MTIA tensor layout formula or a convolution access map.

## 6. Search log
- `Meta Platforms patent MTIA inference accelerator convolution tensor data layout tiling dataflow processing element US assignee`
- `"Meta Platforms" patent tensor "memory layout" OR "sparse" accelerator processing element compiler justia assignee`
