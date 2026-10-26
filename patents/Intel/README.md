# Intel (incl. Movidius, Habana Labs, Altera)

Patents on the three formulas of the kernel address chain:

```
loop counters --(F1) schedule--> output pixels --(F2) access map--> input/weight elements --(F3) layout--> byte address
```

| Tag | Formula | What a patent in this bucket claims |
|---|---|---|
| **F1** | Schedule | loop order, tiling, tile walk, dataflow (weight/output/input stationary), which outputs are computed together |
| **F2** | Access map | how an output (or tile) is turned into input/weight addresses: conv window, stride, pad, dilation, halo, pointer tables, address generators |
| **F3** | Layout | where an element lives: blocked/tiled formats, tile registers, sparse storage, layout conversion |

**Verification legend.** `V` = number, title and assignee confirmed from a Google Patents / Justia / USPTO search result. `P` = number and title confirmed, assignee inferred (inventors, terminology), not confirmed. Claim text was **not** downloaded (see `../README.md`, "Method and limits"); claim summaries come from the indexed abstract and claim snippets. Check the full claims before relying on any of them.

---

## 1. Why Intel matters for your kernel

Intel owns the CPU side of your work: AVX2/AVX-512 (oneDNN `nChw8c` / `nChw16c`), AMX tiles (16 rows x 64 B), the Movidius VPU/NPU (Meteor Lake / Lunar Lake / Arrow Lake NPU), Habana Gaudi, and the Altera FPGA CNN overlays. oneDNN itself is open source under Apache-2.0, which includes a patent licence for the contributed code; Intel's hardware patents below are separate.

The pattern in Intel's portfolio:
- **F1** patents are about *hardware/firmware tile walks* and *schedule-aware data distribution* in an NPU (the Movidius / NPU line), not about CPU loop nests.
- **F2** patents are about *pointer tables* ("storage elements") that let the NPU fetch a conv window without moving data, and about Habana's *tensor descriptors* (base, pad value, strides, sizes in on-chip memory).
- **F3** patents are about *AMX tile configuration* (palette, rows, bytes per row) and *sparse tensor storage*.

---

## 2. Summary table

| # | Number (family) | Title | F | Priority / filed | Status | V |
|---|---|---|---|---|---|---|
| 1 | US11494608B2; cont. US12112251B2, US12223413B2; pub. US20190370631A1; KR20210021258A | Methods and apparatus to tile walk a tensor for convolution operations | F1 + F2 | filed Aug 2019 (16/540,581) | granted | V |
| 2 | US11907827B2; cont. US12288153B2 | Schedule-aware tensor distribution module | F1 + F3 | filed 28 Jun 2019 (16/456,707) | granted 20 Feb 2024; cont. granted 29 Apr 2025 | V |
| 3 | US20240013040A1 | Output drain path facilitating flexible schedule-based deep neural network accelerator | F1 | 2023 | application | P |
| 4 | US11061738B2 (parent), US11675629B2 (cont.), pub. US20220138016A1 | Methods and apparatus to store and access multi-dimensional data | F2 + F3 | parent 16/289,150; cont. 17/373,491 | granted | V (Movidius) |
| 5 | US20220108135A1 -> US12541569B2 | Methods and apparatus for performing a machine learning operation using storage element pointers | F2 | 2021 | granted | V |
| 6 | US20230376765A1 | Performing operation in neural network with storage pointer and sparsity map | F2 | 2023 | application | P |
| 7 | US11940907B2; cont. US20240134786A1 | Methods and apparatus for sparse tensor storage for neural network accelerators | F3 | filed 2021 | granted | V |
| 8 | US11321092B1 | Tensor-based memory access | F2 + F3 | Habana Labs | granted | V (Habana) |
| 9 | US11080048B2; US12282773B2; pub. US20240111533A1 | Systems, methods, and apparatus for tile configuration | F3 | 2017 family (AMX) | granted | V |
| 10 | US11567765B2; US12182571B2 | Systems, methods, and apparatuses for tile load (/ tile load, multiplication and accumulation) | F2 + F3 | 2017 family (AMX) | granted | V |
| 11 | US12106100B2; US11816483B2; WO2018174935A1 | Systems, methods, and apparatuses for matrix operations | F3 | 2017 PCT (AMX) | granted | V |
| 12 | US12147804B2; US11200055B2 | Tile matrix multiplication and accumulation; matrix add, subtract, and multiply | F3 | 2017 family (AMX) | granted | V |
| 13 | US10614354B2 (pub. US20170103299A1); US10726328B2 (pub. US20170103298A1) | Method and apparatus for implementing layers on a CNN accelerator; ... designing and implementing a CNN accelerator | F1 | 2015-16 (Altera) | granted | V (Altera) |
| 14 | US12321857B2 | Methods and apparatus to perform machine-learning model operations on sparse accelerators | F2 | — | granted 3 Jun 2025 | P |

---

## 3. Patent details

### F1 — Schedule

#### 3.1 US11494608B2 — Methods and apparatus to tile walk a tensor for convolution operations
- **Assignee:** Intel Corporation. **Inventors:** Yaniv Fais, Moshe Maor.
- **Family:** US 16/540,581 (filed Aug 2019, pub. US20190370631A1) -> **US11494608B2**; continuation 17/954,846 (filed 28 Sep 2022) -> **US12112251B2** (apparatus for executing a conv layer); continuation 18/392,761 (filed 21 Dec 2023) -> **US12223413B2** (claim 1: a method that partitions an input tensor of a neural-network convolution into tiles; terminal disclaimer). Korean member KR20210021258A.
- **What it covers.** A hybrid firmware/hardware *tile walk*. The input tensor is cut into tiles; **multiple dimensional iterators** drive the accelerator across the tiles; a **tile pointer** locates each tile in local memory. Firmware pads tile data, chooses a work pattern from the tensor size, and sets the convolution stride per tile. Named walk structures: **batch walk, input-reuse walk, weight-reuse walk**.
- **In three-formula terms (analysis).**
  - F1: the "walk" is the loop order over tiles, e.g. `for tile_n, for tile_h, for tile_w, for tile_c ...`, with the walk type deciding which operand stays resident (input reuse = input stationary, weight reuse = weight stationary).
  - F2: the iterators plus stride and padding generate each tile's input window — the same `ih = oh*stride - pad + r` rule, applied per tile, with firmware-inserted padding at tile edges.
  - F3: the tile pointer is an indirection to the tile's local-memory location.
- **Overlap with your kernel.** Your `(n, oh, b, kh, ki, c, jj)` loop nest is a software tile walk on a CPU. The claims are written around an accelerator with firmware plus hardware iterators; the overlap is conceptual (tile walk + reuse choice) rather than structural.
- **Links:** [Google Patents US11494608](https://patents.google.com/patent/US11494608) · [USPTO PDF 11494608](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11494608) · [USPTO PDF 12112251](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12112251) · [USPTO PDF 12223413](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12223413) · [Justia US20190370631](https://patents.justia.com/patent/20190370631) · [KR20210021258A](https://patents.google.com/patent/KR20210021258A/en) · [OG notice 12112251](https://patentsgazette.uspto.gov/week41/OG/html/1527-2/US12112251-20241008.html) · [OG notice 12223413](https://patentsgazette.uspto.gov/week06/OG/html/1531-2/US12223413-20250211.html)

#### 3.2 US11907827B2 — Schedule-aware tensor distribution module
- **Assignee:** Intel Corp. **Inventors:** Gautham Chinya, Huichu Liu, Arnab Raha, Debabrata Mohapatra, Cormac Brick, Lance Hacking.
- **Family:** 16/456,707 filed 28 Jun 2019, granted 20 Feb 2024; continuation 18/408,716 filed 10 Jan 2024 -> **US12288153B2** granted 29 Apr 2025 (20 claims).
- **What it covers.** An NN accelerator with many processing engines (PEs) and **schedule-aware tensor distribution circuitry** that loads tensor data into the PEs, extracts the outputs, **reorganizes** them, and writes them back to memory. The stated problem: adjacent DNN layers have different optimal schedules, and writing layer *i*'s output in layer *i*'s order causes bank conflicts when layer *i+1* reads it in its own order.
- **In three-formula terms (analysis).** This is the hardware form of the F1/F3 coupling: the producer's schedule (F1) fixes the order in which outputs appear; the distribution module re-lays them out (F3) to match the consumer's schedule. It is the same reason your `nChw8c` output is "the next layer's `mimo` input".
- **Related paper:** FlexNN, Intel, [arXiv 2403.09026](https://arxiv.org/abs/2403.09026) (software-configured descriptors choose the dataflow per layer).
- **Links:** [Google Patents US11907827](https://patents.google.com/patent/US11907827) · [Justia 11907827](https://patents.justia.com/patent/11907827) · [USPTO PDF 11907827](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11907827) · [USPTO PDF 12288153](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12288153) · [OG notice 12288153](https://patentsgazette.uspto.gov/week17/OG/html/1533-5/US12288153-20250429.html)

#### 3.3 US20240013040A1 — Output drain path facilitating flexible schedule-based DNN accelerator
- **Assignee:** Intel (same FlexNN line; `P`).
- **What it covers.** The output side of the flexible-schedule accelerator: an output "drain" path that collects PE results in whatever order the chosen schedule produces them.
- **Link:** [Justia 20240013040](https://patents.justia.com/patent/20240013040)

#### 3.4 US10614354B2 / US10726328B2 — Altera CNN accelerator layers (Altera, now separate from Intel)
- **Assignee:** Altera Corporation (Intel acquired Altera in 2015; check the USPTO assignment database for the current owner). **Inventors:** Utku Aydonat, Gordon Raymond Chiu, Andrew Chaang Ling.
- **US10614354B2** (app. 15/017,597, pub. US20170103299A1): implement a CNN accelerator on an FPGA using PEs for a standard convolution layer, and **change the data flow** so the same PEs implement a fully-connected layer.
- **US10726328B2** (pub. US20170103298A1): designing and implementing a CNN accelerator (FPGA overlay generation).
- **In three-formula terms.** F1: the same PE array with a re-routed schedule for conv vs FC.
- **Links:** [Justia 10614354](https://patents.justia.com/patent/10614354) · [Google Patents US10614354](https://patents.google.com/patent/US10614354) · [USPTO PDF 10614354](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10614354) · [USPTO PDF 10963777](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10963777) (same title, continuation) · [Google Patents US20170103298A1](https://patents.google.com/patent/US20170103298A1/en) · [USPTO PDF 10726328](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10726328)

### F2 — Access map

#### 3.5 US11061738B2 / US11675629B2 — Methods and apparatus to store and access multi-dimensional data (Movidius)
- **Assignee:** Movidius (Intel subsidiary). **Inventors:** Fergal Connor, David Bernard, Niall Hanrahan, Derek Harnett.
- **Family:** parent 16/289,150 -> US11061738; continuation 17/373,491 -> **US11675629B2** (pub. US20220138016A1).
- **What it covers.** Multi-dimensional data is split into **storage elements**; a **pointer array** holds the start address of each storage element. Claims of US11675629: determine a storage-element coordinate, apply a pointer offset as an index into the **storage element pointer array**, and obtain the activation value.
- **In three-formula terms (analysis).** This splits F3 into two levels: `address = SE_pointer[se_coord] + offset_within_SE`. The pointer table means storage elements need not be contiguous — the same idea as Qualcomm's crouton chunks behind a pointer table. F2 then becomes "compute `se_coord` and in-element offset from (n, h, w, c)".
- **Links:** [Google Patents US11675629](https://patents.google.com/patent/US11675629) · [USPTO PDF 11675629](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11675629) · [USPTO PDF 11061738](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11061738) · [Justia 20220138016](https://patents.justia.com/patent/20220138016) · [Inventor list, Fergal Connor](https://patents.justia.com/inventor/fergal-connor)

#### 3.6 US20220108135A1 -> US12541569B2 — Machine learning operation using storage element pointers
- **Assignee:** Intel. **What it covers.** A pointer table in CMX memory used for standard, group and **dilated** convolutions. Claims: create first and second storage element pointers **based on the type of ML operation**, and **remap tensor data without moving it in memory**.
- **In three-formula terms (analysis).** This patents doing F2 through pointer rewriting: a dilated or grouped conv is expressed by a different pointer table over the same stored data, instead of a different address formula in the kernel.
- **Links:** [Justia 20220108135](https://patents.justia.com/patent/20220108135) · [USPTO PDF 12541569](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12541569)

#### 3.7 US20230376765A1 — Performing operation in neural network with storage pointer and sparsity map
- **Assignee:** Intel (`P`). Uses **sparsity maps plus storage pointers** to execute transposed, resized and dilated convolutions on a PE array.
- **Link:** [Justia 20230376765](https://patents.justia.com/patent/20230376765)

#### 3.8 US12321857B2 — Machine-learning model operations on sparse accelerators
- **Assignee:** Intel (`P`). Granted claim 1: circuitry plus a buffer holding sparsity data, with sparsity data handled differently per convolution type.
- **Link:** [OG notice 12321857](https://patentsgazette.uspto.gov/week22/OG/html/1535-1/US12321857-20250603.html)

#### 3.9 US11321092B1 — Tensor-based memory access (Habana Labs)
- **Assignee:** Habana Labs Ltd. **Assignors:** Amos Goldman, Shlomo Raikin, Evgeny Spektor, Ran Halutz, Sergei Gofman, Ron Shalev.
- **What it covers.** A processor whose internal memory stores the **definition of a multi-dimensional array** held in external memory, plus **indices given as multi-dimensional coordinates**. A **Tensor Descriptor Memory** holds base address, **padding value**, dimension offsets, **strides** and **sizes**.
- **In three-formula terms (analysis).** The descriptor is F3 (base + strides + sizes) and the coordinate interface is F2: software issues `(n, h, w, c)` and the hardware applies the layout, returning the padding value for out-of-range coordinates — which is exactly the `ih = oh - 1 + kh` boundary case handled by hardware rather than by the kernel. Gaudi's TPC "index space" (each member processes 64 elements) is the matching F1 ([TPC Programming Model](https://docs.habana.ai/en/latest/TPC/TPC_User_Guide/TPC_Programming_Model.html)).
- **Links:** [Google Patents US11321092](https://patents.google.com/patent/US11321092) · [Justia 11321092](https://patents.justia.com/patent/11321092)

### F3 — Layout

#### 3.10 US11940907B2 — Sparse tensor storage for neural network accelerators
- **Assignee:** Intel Corp (filed 2021). Continuation US20240134786A1.
- **What it covers.** A compressed storage format for sparse tensors on the NPU (non-zero values plus a sparsity bitmap, laid out per storage element).
- **Links:** [Google Patents US11940907](https://patents.google.com/patent/US11940907) · [USPTO PDF 11940907](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11940907) · [Justia 20240134786](https://patents.justia.com/patent/20240134786)

#### 3.11 AMX tile family — tile configuration, tile load, matrix operations
These are the patents behind AMX, the 16-row x 64-byte tile registers that set oneDNN's `OhwI16i64o4i` weight block.

| Number | Title | Key content |
|---|---|---|
| US11080048B2; US12282773B2; US20240111533A1 | Systems, methods, and apparatus for tile configuration | A **palette** lists tile options (number of tile names, **bytes per row**, rows and columns per tile); max tile height = architected storage / (palette names x bytes per row); a TILECONFIG instruction reads a description from memory and selects a tile usage; consistency checks at execution |
| US11567765B2 | Systems, methods, and apparatuses for tile load | TILELOAD with **strided** addressing: base + displacement, stride from an index register shifted by a scale |
| US12182571B2 | ... tile load, multiplication and accumulation | same strided TILELOAD addressing, combined with multiply-accumulate |
| US12106100B2; US11816483B2; WO2018174935A1 | Systems, methods, and apparatuses for matrix operations | decode an instruction naming a destination matrix (tile) and a memory source; load **groups of strided data elements into configured rows** |
| US12147804B2 | ... tile matrix multiplication and accumulation | Intel Corp; TMUL operation |
| US11200055B2 | ... matrix add, subtract, and multiply | tile arithmetic |

- **In three-formula terms (analysis).** F3 at the register level: a tile is `rows x bytes_per_row`, loaded row by row with `addr(row) = base + row*stride`. The memory side is a plain 2-D strided layout; the *blocking* (16 rows, 64 B, VNNI groups of 4 int8 / 2 bf16 along K) is forced by the tile shape and lives in the prepacked weights.
- **Overlap with your kernel.** None for AVX2 fp32 (no tiles, no VNNI). Relevant only if you move to AMX.
- **Links:** [Google Patents US11080048](https://patents.google.com/patent/US11080048) · [Justia 12282773](https://patents.justia.com/patent/12282773) · [Justia 20240111533](https://patents.justia.com/patent/20240111533) · [USPTO PDF 11567765](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11567765) · [USPTO PDF 12182571](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12182571) · [Google Patents US12106100](https://patents.google.com/patent/US12106100) · [Google Patents US11816483](https://patents.google.com/patent/US11816483) · [WO2018174935A1](https://patents.google.com/patent/WO2018174935A1/en) · [Google Patents US12147804](https://patents.google.com/patent/US12147804) · [Google Patents US11200055](https://patents.google.com/patent/US11200055) · [USPTO PDF 12260213](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12260213) · ISA reference: [LDTILECFG](https://www.felixcloutier.com/x86/ldtilecfg)

---

## 4. What was searched and not found

- **No Intel patent was found that claims the oneDNN blocked activation formats** (`nChw8c`, `nChw16c`, `OIhw8i8o`) as such. Blocked channel layouts were public (oneDNN / MKL-DNN source, 2016 onward; [arXiv 1808.05567](https://arxiv.org/abs/1808.05567) "Anatomy of High-Performance Deep Learning Convolutions on SIMD Architectures", Intel authors, 2018) — this public record matters as prior art.
- **No Intel patent was found on compiler loop-tiling / auto-scheduling for tensor ops** or on oneDNN Graph layout propagation (searches returned papers only, e.g. [arXiv 2301.01333](https://arxiv.org/abs/2301.01333)).
- An Intel EP filing "packed data convolution instruction with shift control and width control" (EP4109244A1) appeared only in a citation list; not examined.

## 5. Search log
- `Intel Corporation patent loop tiling schedule convolution neural network accelerator output tile register blocking`
- `Intel patent address generation convolution input window stride padding tensor accelerator`
- `Intel patent tensor memory layout data format blocked channels matrix tile AMX storage format`
- `"Intel" "Methods and apparatus to tile walk a tensor for convolution operations" US patent`
- `US11907827B2 "Schedule-aware tensor distribution module" assignee Intel claims`
- `Intel patent tile configuration "rows" "bytes per row" palette matrix tile register TILECFG`
- `Habana Labs patent tensor processing "index space" tensor addressing descriptor stride TPC`
- `Intel Movidius patent "storage element" pointer table convolution input activation sparse NCE layout`
- `Intel patent "systems, methods, and apparatuses for matrix operations" tile load stride TMUL`
- `US20170103299A1 "Implementing Layers on a Convolutional Neural Network Accelerator" Altera assignee`
