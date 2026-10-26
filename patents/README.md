# Vendor patents on the three formulas of a convolution/GEMM kernel

This folder collects patent research for ~30 companies on the **three formulas** that turn loop counters into a byte address, the framework from `Vendor_tensor_layouts_and_offsets_2.md`:

```
loop counters ──(F1) schedule──► output pixels ──(F2) access map──► input/weight elements ──(F3) layout──► byte address
```

| Tag | Formula | Decided by | Patent bucket |
|---|---|---|---|
| **F1** | **Schedule** | kernel/compiler designer (1x3, 2x4, tile walk, dataflow, loop order, layer fusion) | loop tiling, tile-size selection, dataflow (weight/output/input stationary), tile walks, static scheduling, polyhedral affine schedules |
| **F2** | **Access map** (the conv rule) | the operation: `ih = oh·s − p + r`, `iw = ow·s − p + s'` | address generators, im2col/implicit-GEMM, stride/dilation/transpose handling, pointer tables, DMA descriptors, line/window buffers |
| **F3** | **Layout** | the storage choice (nchw, Ohwi8o, nChw8c, NC1HWC0, tiles, runnels) | blocked/tiled formats, tile registers, sparse storage, swizzle/banking, layout propagation & conversion |

The offset at the end of the chain is always `offset = sum_i digit_i * stride_i` with strides a running product of padded digit sizes. Every company below is doing the same three things with different numbers — exactly the conclusion of the source file.

---

## Method and limits  (read this before trusting any number)

**How the research was done.** Patents were found by keyword search over the public Google Patents / Justia / USPTO full-text index (the WebSearch tool). The patent **PDFs and full claim texts were not downloaded** — the egress policy in this environment blocks the patent hosts directly (patents.google.com, ppubs.uspto.gov, Espacenet, Lens all returned 403/DNS failures; the Google Patents BigQuery public dataset was also not reachable). So:

- **Numbers, titles, assignees, inventors, priority/grant dates** come from search-result snippets of those pages. Where a result stated the assignee, the entry is marked **`V`** (verified-in-result). Where only the number+title were confirmed and the assignee is inferred from inventors/terminology/family, it is marked **`P`**.
- **Claim summaries** are from the indexed abstract and claim snippets, **not** from reading the granted claims. Treat every "what it covers" as a lead to verify, not a legal statement.
- **"In three-formula terms (analysis)"** blocks are *my* mapping of the patent onto F1/F2/F3, written to help you place it against your kernel. They are analysis, not quotes.
- Google Patents' own legal-status and assignee fields carry a disclaimer that they are assumptions; several patents here are marked expired/fee-related by Google and should be re-checked on USPTO.

**This is not a freedom-to-operate opinion or legal advice.** Before relying on any entry — especially the layout patents most relevant to your kernel (Magic Leap US10489680, Qualcomm US12165237, AMD US10762392, IBM NNPA, Huawei US12306901) — read the actual granted claims and consult a patent attorney. Nothing here clears or blocks anything.

**Prior-art note that matters for you.** The specific layout triple your kernel uses — `nchw` input (C=3), `Ohwi8o` weights, `nChw8c` output on AVX2 — is the oneDNN `jit_avx2` `flat` branch, open source under Apache-2.0 since MKL-DNN ~2016, and described in Intel's own 2018 paper ([arXiv 1808.05567](https://arxiv.org/abs/1808.05567)). That public record is strong prior art: using these standard blocked layouts is well-trodden ground. Risk, if any, attaches to *novel* packing/scheduling methods you might invent, not to adopting the documented oneDNN formats.

---

## Companies covered

**The 9 you named:** [Intel](Intel/) · [IBM](IBM/) · [NVIDIA](NVIDIA/) · [d-Matrix](d-Matrix/) · [Etched](Etched/) · [Groq](Groq/) · [AMD](AMD/) (incl. Xilinx) · [SiMa.ai](SiMa.ai/) · [Qualcomm](Qualcomm/) (incl. [Reservoir Labs](Reservoir_Labs/))

**Datacenter accelerators:** [Google](Google/) (TPU) · [Amazon](Amazon/) (Trainium/Inferentia) · [Huawei](Huawei/) (Ascend) · [Tenstorrent](Tenstorrent/) · [SambaNova](SambaNova/) · [Graphcore](Graphcore/) · [Cerebras](Cerebras/) · [Tesla](Tesla/) (Dojo) · [Cambricon](Cambricon/) · [Meta](Meta/) (MTIA) · [Microsoft](Microsoft/) (Maia)

**Edge / mobile / IP:** [Apple](Apple/) · [Arm](Arm/) · [Samsung](Samsung/) · [Magic Leap](Magic_Leap/) · [Hailo](Hailo/) · [Rockchip](Rockchip/) · [Cadence/Synopsys/Ceva](IP_vendors_Cadence_Synopsys_Ceva/) · [Mythic/FuriosaAI/Rebellions](Mythic_Furiosa_Rebellions/)

---

## Cross-company map: which formula each company's strongest patents target

| Company | F1 schedule | F2 access map | F3 layout | Single most on-point patent |
|---|:---:|:---:|:---:|---|
| Intel | ●● | ●● | ●● | US11494608 tile walk; AMX tile-config family |
| NVIDIA | ●● | ●●● | ●● | US12141082 TMA coord->address (+im2col) |
| AMD/Xilinx | ●● | ●●● | ●● | US10762392 tiling format; US10984500 inline im2col |
| Qualcomm | ●●● | ● | ●● | US12165237 RGB first-layer blocked format |
| Reservoir Labs (→Qualcomm) | ●●● | ●● | ●● | US10180828 JPLCVD; US8661422 local memory compaction |
| IBM | ● | ● | ●●● | NNPA 4D-feature layout; US5953531 tile-size |
| Google (TPU) | ●● | ●●● | ● | US9875104 tensor traversal (index×multiplier) |
| Amazon | ●● | ●●● | ●● | stride/dilation/transposed-conv systolic families |
| Huawei | ● | ●● | ●●● | US12306901 NC1HWC0 + matrix converter |
| Groq | ●●● | ●● | ● | US11455370 flattened-input + expanded-kernel |
| Tenstorrent | ●● | ○ | ●(docs) | US12248430 NoC data movement |
| SambaNova | ●● | ●● | ●●● | US12093551 memory-unit partitioning; US11645057 |
| Graphcore | ●● | ○ | ●● | US11449254 managed bulk memory (layout convert) |
| Cerebras | ?(stub) | ? | ? | none pinned |
| Tesla | ● | ●● | ●● | US11157441 non-consecutive data formatter |
| Cambricon | ● | ● | ●● | US11836491 tensor descriptor (shape+address) |
| Meta | ● | ○ | ● | US20190325303 accelerator arch |
| Microsoft | ●● | ● | ●● | US12572341 tile-config compilation; block-FP conv |
| Apple | ●● | ● | ●(docs) | US11853868 multi-dim conv, accumulator batches |
| Arm | ● | ● | ●●●(docs) | ACL OHWIo formula (open); US11275996 format params |
| Samsung | ● | ●● | ●● | US11971823 cyclic reuse buffer; PIM data placement |
| Magic Leap | ○ | ● | ●●● | **US10489680 runnel tile kernel layout** |
| SiMa.ai | ●●● | ● | ● | US11488066 multi-kernel conv decomposition |
| d-Matrix | ●● | ○ | ○ | US11847072 DIMC transformer accel |
| Etched | ●● | ○ | ○ | multi-chip systolic; model-specific compile |

(●●● strong / ●● present / ● light / ○ not found / ?(stub) not yet searched. Dot counts are a reading aid, not a claim-strength metric.)

---

## The same three numbers, across vendors (from the source file + patents)

The whole industry differs in just three numbers: the **block size**, the **split axis**, and whether the reduction needs a **sub-group**. Block size = width of one hardware op:

| Vendor | Activation block | Reason |
|---|---|---|
| Intel AVX2 (your kernel) | 8 fp32 | 256-bit ymm |
| Intel AVX-512 | 16 fp32 | 512-bit zmm = cache line |
| Arm NEON | 4 fp32 | 128-bit q-register |
| NVIDIA tensor core | 8 fp16 | 128-bit load |
| Huawei Ascend C0 | 16 (fp16/fp32) / 32 (int8) | Cube 32 B x 32 B |
| IBM NNPA | 64-element vector | NNP vector |
| Rockchip | 16 bytes (C2) | MAC cell caches 16 weight bytes |
| Tenstorrent | 16x16 face (32x32 tile) | engine multiplies 16x16 |
| Google TPU | 8x128 | 32-bit vector registers |
| Qualcomm HMX | 32x32 crouton | HMX tile |

Sub-group (the trailing `4i`/`2i`) exists only when the datatype is narrower than the lane: 4 int8 or 2 bf16 per 32-bit lane. **fp32 has granularity 1 — so your AVX2 fp32 kernel is the simplest layout case that exists** (source file, section 9 and conclusion).

---

## Relevance to the UG_LC-AP-L kernel (your AVX2 fp32 2x4 tile)

Ordered by how directly a patent family touches your exact design:

1. **Magic Leap US10489680** — reorder weights into SIMD-width "runnels" + interleaved activations. Your `Ohwi8o` + `nChw8c` are the same shapes. Read its claims before publishing any novel packing. → [Magic_Leap](Magic_Leap/)
2. **Qualcomm US12165237** — blocked storage for the **3-channel RGB first layer**, the exact `C=3` case your kernel handles with plain `nchw`. → [Qualcomm](Qualcomm/)
3. **AMD US10762392** — tiling format with a spatial block carrying a **halo** (18x34 block feeds 16x32 outputs of a 3x3 conv) — the "output tile decides input window" idea, baked into layout. → [AMD](AMD/)
4. **Intel `jit_avx2` (open source, not a patent)** — your exact layout triple; the prior-art anchor. → [Intel](Intel/)
5. **Google US9875104 / US10534607** — `offset = Σ index·multiplier` and the adder (strength-reduced pointer increment) form — your formula (3) and the "+12 B per block" increments, in hardware. → [Google](Google/)
6. **Samsung US11971823** — cyclic reuse buffer for overlapping windows = your 2x4 tile's row reuse (2 of 3 input rows shared). → [Samsung](Samsung/)
7. **Amazon stride/dilation/transpose families; Tesla US11157441 non-consecutive gather** — the access map (row-contiguous, row-pitch jump) in hardware. → [Amazon](Amazon/), [Tesla](Tesla/)
8. **IBM NNPA; Huawei NC1HWC0** — `nChw{64|16}c`-style blocked layouts with padding, the fp32/fp16 cousins of your `nChw8c` output. → [IBM](IBM/), [Huawei](Huawei/)
9. **Reservoir Labs/Qualcomm JPLCVD; SambaNova partitioning; IBM US5953531; Microsoft US12572341** — the F1 side: tile-size selection and schedule-vs-layout joint optimisation, i.e. your 1x3→2x4 decision automated. → [Reservoir_Labs](Reservoir_Labs/), [SambaNova](SambaNova/), [IBM](IBM/), [Microsoft](Microsoft/)

Your kernel has, as the source file concludes, "essentially no layout freedom to get wrong beyond choosing 8 and aligning to 64 bytes" — and the patent landscape confirms it: the dense layout patents are about blocks, sub-groups and swizzles that **fp32 on a CPU simply does not need**.

---

## Status of coverage
- All 9 named companies: done, with patent-level detail.
- Datacenter + edge majors: done. Cerebras and the Mythic/Furiosa/Rebellions/IP-vendor folders are **partial/stubs** (searches returned product material, not pinned patents) — each lists the exact assignee searches still to run.
- Every folder ends with its own search log so the research can be reproduced or extended.
