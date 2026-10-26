# Google (TPU, XLA)

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = number, title, assignee confirmed; `P` = assignee inferred (inventors, family).

---

## 1. Why Google matters for your kernel

Google's TPU portfolio (2015-2017 filings, many by Jonathan Ross, Andrew Phelps, Dong Hyuk Woo, Olivier Temam, Ravi Narayanaswami) has the **cleanest hardware patents on F2** in this study: the **tensor traversal unit** that turns nested-loop indices into an address with **tensor index elements x dimension multiplier elements** — literally `addr = sum_i index_i * stride_i`, i.e. your formula (3) evaluated by hardware, driven by formula (1)'s loop counters. A separate family handles **stride > 1 convolution** by running a stride-1 layer and masking.

The F3 side — XLA's tiled layout `T(8,128)`, `(8,128)(2,1)` for bf16 — is documented in the open ([OpenXLA tiled layout](https://openxla.org/xla/tiled_layout)) but **no patent on it was found**.

---

## 2. Summary table

| # | Number (family) | Title | F | Priority / filed | Status | V |
|---|---|---|---|---|---|---|
| 1 | US9875104B2 (app. 15/014,265, filed 3 Feb 2016); US9875100; US10228947 (cont. via 15/456,812, 13 Mar 2017); US10838724B2; EP3226121A3; SG10201610917TA | Accessing data in multi-dimensional tensors | **F2 + F3** | 3 Feb 2016 | granted | V |
| 2 | US10534607B2 | Accessing data in multi-dimensional tensors using adders | **F2** | — | granted | V |
| 3 | US10248908 | Alternative loop limits for accessing data in multi-dimensional tensors | F1 + F2 | — | granted | P |
| 4 | US10802956B2 | Accessing prologue and epilogue data | F2 | — | granted | P |
| 5 | US9959498B1 (app. 15/336,216); US9836691B1 (app. 15/455,685, 10 Mar 2017); US9846837; US12061968 | Neural network instruction set architecture | **F1 + F2** | — (15/455,685 filed 10 Mar 2017) | granted | V |
| 6 | US11816532; WO2018089079A1; EP4336411A3; GB2556670B; IE20170205A1; SG10201707700WA | Performing kernel striding in hardware | **F2** | — | granted | V |
| 7 | US9697463B2 (pub. US20170103316A1; parent 14/844,738, 3 Sep 2015; prov. 21 May 2015) | Computing convolutions using a neural network processor | F1 + F2 | 21 May 2015 | granted | V |

---

## 3. Patent details

### F2 — Access map (the tensor traversal unit)

#### 3.1 US9875104B2 / US10228947 / US10838724B2 / US9875100 — Accessing data in multi-dimensional tensors
- **Inventors (US9875104):** Dong Hyuk Woo, Andrew Everett Phelps. App. 15/014,265 filed 3 Feb 2016. US10228947 continues 15/456,812 (13 Mar 2017), itself a continuation of 15/014,265. Foreign: EP3226121A3, SG10201610917TA.
- **What it covers (abstract / claims of US10228947).** An apparatus with **multiple tensor index elements** and **multiple dimension multiplier elements** obtains an instruction to access a particular element of an N-dimensional tensor, **determines its address from the tensor index elements and the dimension multiplier elements**, and outputs the address (optionally as an offset from another element).
- **In three-formula terms (analysis).** This is exactly `offset = sum_i (digit_i * stride_i)` from your vendor file, in hardware: the index registers hold the loop counters (F1's output), the multiplier registers hold the strides (F3), and the conv window shift (F2) is applied by how the loops are set up. It is the hardware version of oneDNN's `off_v()` and of your three "addr =" lines.
- **Overlap with your kernel (technical note).** Your kernel computes the same dot product, but in software (JIT/compile-time constants and pointer increments), on a general-purpose CPU. The claims are for dedicated index/multiplier hardware elements.
- **Links:** [Google Patents US9875104B2](https://patents.google.com/patent/US9875104B2/enExample) · [USPTO PDF 9875104](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/9875104) · [USPTO PDF 9875100](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/9875100) · [Justia 10228947](https://patents.justia.com/patent/10228947) · [USPTO PDF 10228947](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10228947) · [Google Patents US10838724](https://patents.google.com/patent/US10838724) · [EP3226121A3](https://patents.google.com/patent/EP3226121A3/enExample) · [EP3226121 document](https://data.epo.org/publication-server/rest/v1.2/patents/EP3226121NWA3/document.html)

#### 3.2 US10534607B2 — Accessing data in multi-dimensional tensors using adders
- **Inventors:** Olivier Temam, Harshit Khaitan, Ravi Narayanaswami, Dong Hyuk Woo.
- **What it covers.** The address is built incrementally: a **partial address offset per loop dimension** is updated with **adders** as each loop advances, instead of multiplying index x stride.
- **In three-formula terms (analysis).** The strength-reduced form of the dot product: when loop `i` steps, add `stride_i`; when it wraps, subtract `extent_i * stride_i` and step the next loop. This is exactly how a JIT kernel turns "the parts that change with b, oh and kh" into pointer additions (+12 B per block, +512 B per filter row in your file).
- **Overlap with your kernel (technical note).** Pointer increments in software loops are textbook compiler strength reduction (decades old); the claims are for a hardware traversal unit with per-dimension adders.
- **Links:** [Google Patents US10534607](https://patents.google.com/patent/US10534607)

#### 3.3 US10248908 — Alternative loop limits for accessing data in multi-dimensional tensors
- Compute tiles run a nested loop to traverse an N-dimensional tensor; **alternative loop limits** let the last iteration of a loop use a different bound (the tail block — your last `b` block with 2 pixels instead of 3). **Link:** [Justia 10248908](https://patents.justia.com/patent/10248908)

#### 3.4 US10802956B2 — Accessing prologue and epilogue data
- An address offset is adjusted after each address determination so that data before/after the main loop (prologue/epilogue) is reached by the same traversal hardware. **Link:** [Google Patents US10802956](https://patents.google.com/patent/US10802956)

#### 3.5 US11816532 (family WO2018089079A1) — Performing kernel striding in hardware
- **What it covers (claims summary).** Receive a request to run a network with a **conv layer of stride > 1** on a hardware circuit that natively does stride 1: run an **equivalent stride-1 layer**, **zero out the outputs the strided layer would skip** (masking), then apply **max pooling** to obtain the strided result. Inputs in a unified buffer, weights in dynamic memory, compute on the matrix unit.
- **In three-formula terms (analysis).** F2 with stride `s` rewritten as F2 with stride 1 followed by a sub-sampling of the output index space: `oh_s = oh_1 / s` where `oh_1 % s == 0`.
- **Links:** [Justia 11816532](https://patents.justia.com/patent/11816532) · [WO2018089079A1](https://patents.google.com/patent/WO2018089079A1/cg-RX-API-DMAC7.html) · [EP4336411A3](https://patents.google.com/patent/EP4336411A3/enExample) · [GB2556670B](https://patents.google.com/patent/GB2556670B/enExample) · [IE20170205A1](https://patents.google.com/patent/IE20170205A1/cg-RX-API-DMAC7.html) · [SG10201707700WA](https://patents.google.com/patent/SG10201707700WA/enExample)

#### 3.5b US11314674 / US11762793B2 / US12287745B2 — Direct memory access architecture with multi-level multi-striding
- **Assignee:** Google LLC. **Inventors:** Mark William Gottscho, Matthew William Ashcraft, Thomas Norrie, Oliver Edward Bowen. Provisional Feb 2020; US11762793 from app. 17/728,478 (filed 25 Apr 2022, continuation); US12287745 granted 29 Apr 2025; EP4022450.
- **What it covers.** DMA hardware in which each DMA thread has a **request generator that produces m memory addresses for a multi-dimensional tensor in one cycle**, using m memory address units, with **multi-level, multi-stride** address patterns.
- **In three-formula terms (analysis).** F3's dot product evaluated m times in parallel per cycle — the DMA analogue of the tensor traversal unit above.
- **Links:** [Google Patents US11762793](https://patents.google.com/patent/US11762793) · [USPTO PDF 11762793](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11762793) · [USPTO PDF 11314674](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11314674) · [Justia 12287745](https://patents.justia.com/patent/12287745) · [OG 12287745](https://patentsgazette.uspto.gov/week17/OG/html/1533-5/US12287745-20250429.html) · [EP4022450](https://data.epo.org/publication-server/rest/v1.2/patents/EP4022450NWA1/document.html)

#### 3.5c US10504022B2 / US11727259B2 / US11501144 — Neural network accelerator with parameters resident on chip
- **Assignee:** Google LLC. **Inventors:** Olivier Temam, Harshit Khaitan, Ravi Narayanaswami, Dong Hyuk Woo. Provisional 62/544,171 (11 Aug 2017).
- **What it covers.** A compute tile with a **first memory bank for input activations** and a **second memory bank holding enough network parameters on-chip** to meet latency/throughput targets, both feeding the MAC operators.
- **In three-formula terms (analysis).** F3 placement: keep weights resident (weight-stationary storage), so F2's weight address never leaves the chip — the on-chip analogue of your "weights are 864 bytes, trivially resident in L1".
- **Links:** [Google Patents US11727259](https://patents.google.com/patent/US11727259) · [USPTO PDF 10504022](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10504022) · [Justia 10504022](https://patents.justia.com/patent/10504022)

### F1 — Schedule

#### 3.6 US9959498B1 / US9836691B1 / US9846837 / US12061968 — Neural network instruction set architecture
- **Family:** US9959498B1 (app. 15/336,216); US9836691B1 (app. 15/455,685 filed 10 Mar 2017, continuation); US9846837; US12061968 (later continuation listing the chain).
- **What it covers.** A processing unit receives **an instruction that specifies data values for a tensor computation** and executes a **loop nest whose structure is defined by those values** (loop bounds, strides, tensor locations); the tensor traversal unit generates the addresses.
- **In three-formula terms (analysis).** F1 encoded in an instruction: the instruction carries the loop-nest description (your `n, oh, b, kh, ki, c, jj` with bounds), and the hardware runs it.
- **Links:** [Google Patents US9959498](https://patents.google.com/patent/US9959498) · [Justia 9846837](https://patents.justia.com/patent/9846837) · [Justia 12061968](https://patents.justia.com/patent/12061968)

#### 3.7 US9697463B2 — Computing convolutions using a neural network processor
- **Assignee:** Google LLC. **Inventors:** Jonathan Ross, Andrew Everett Phelps. Pub. US20170103316A1; continuation of 14/844,738 (filed 3 Sep 2015), provisional 21 May 2015.
- **What it covers.** A **systolic array** where **kernel data enters cells along one dimension and activation data along the second dimension**; the array's accumulated output becomes the layer output.
- **In three-formula terms (analysis).** F1 spatial mapping: output channel `k` -> array column, reduction `(c, r, s)` -> time, with F2 supplying the activation stream for each tap.
- **Links:** [Google Patents US9697463](https://patents.google.com/patent/US9697463) · [Justia 20170103316](https://patents.justia.com/patent/20170103316) · [Golden US9697463](https://golden.com/wiki/US_Patent_9697463_Computing_convolutions_using_a_neural_network_processor-REEV6X9)

---

## 4. Public (non-patent) Google material on F3
- XLA tiled layout, formula `linear_index_with_tile(e, d, t) = linear_index((floor(e/t), e mod t), (ceil(d/t), t))`, tiles `T(8,128)`, `(8,128)(2,1)` bf16, `(8,128)(4,1)` int8: [OpenXLA tiled layout](https://openxla.org/xla/tiled_layout)
- XNNPACK weight packing (bias first, `nr`/`kr` blocks): [packing.cc](https://github.com/google/XNNPACK/blob/master/src/reference/packing.cc)

## 5. Searched and not found
- No Google patent found on XLA **layout assignment** or the **TPU tiled layout** itself. (US11645057B2 "memory layout determination and conflict resolution" surfaced but its assignee was not shown; its dataflow-graph / operation-unit language points elsewhere — see [`../SambaNova/README.md`](../SambaNova/README.md).)
- "Dilated convolution using systolic array" (US11379555B2) is **Amazon's**, not Google's — see [`../Amazon/README.md`](../Amazon/README.md).

## 6. Search log
- `Google patent "Neural network instruction set architecture" US9836691B1 tensor traversal loop nest address calculation claims`
- `Google LLC patent "accessing data in multi-dimensional tensors" loop index address offset hardware adder tensor traversal unit`
- `Google patent TPU tiled layout "tiling" tensor memory layout minor-to-major XLA compiler layout assignment patent`
- `Google patent "Performing kernel striding in hardware" OR "dilated convolution" systolic array neural network processor`
- `US10228947 OR US9875104 "Accessing data in multi-dimensional tensors" Google claims "dimension multiplier"`
- `Google patent "Computing convolutions using a neural network processor" US9697463 Ross claims`
