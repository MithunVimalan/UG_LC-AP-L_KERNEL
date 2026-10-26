# Magic Leap

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred.

---

## 1. Why Magic Leap matters for your kernel  (high relevance)

Magic Leap's **US10489680B2 "Efficient data layouts for convolutional neural networks"** is one of the most **directly on-point F3 patents** in this whole study, and it names the problem exactly as your vendor file does: a naive convolution underuses the SIMD unit unless data is duplicated or convolutions are bundled. Its solution is a **tile kernel layout built from "runnels"** (an ordered list of weight values with two defined properties — the SIMD-width vector) plus an **interleaved input activation layout** and an **interleaved output layout**. That is the same recipe as your `Ohwi8o` weights + channel-blocked activations, under different names.

This folder is **worth reading before you finalise your own weight/input packing**, because its independent claims may cover the general "reorder weights into SIMD-width tiles and interleave the input" method.

---

## 2. Summary table

| # | Number (family) | Title | F | Priority / filed | Status | V |
|---|---|---|---|---|---|---|
| 1 | US10489680B2 (pub. US20180096226A1, app. 15/724,142); cont. US11182645B2; US11720800B2; CN110073359A; JP2019535079A | Efficient data layouts for convolutional neural networks | **F3 (+F2)** | prov. 62/403,930, 4 Oct 2016; filed 3 Oct 2017 | granted 26 Nov 2019 | V |

---

## 3. Patent details

### 3.1 US10489680B2 — Efficient data layouts for convolutional neural networks
- **Assignee:** Magic Leap, Inc. **Inventors:** Ashkan Aliabadi, Gregory David Roberts. Provisional 62/403,930 (4 Oct 2016); app. 15/724,142 (3 Oct 2017, pub. US20180096226A1); continuations US11182645B2, US11720800B2; foreign CN110073359A, JP2019535079A.
- **Problem stated.** "Naive implementations of a convolution may not achieve full utilization of the SIMD execution unit ... without one or both of data duplication or ... bundling convolutions together."
- **What it covers (abstract / application claims).**
  - **Kernel weights -> tile kernel layout:** weights reordered into **kernel tiles**, each tile made of **kernel runnels** — "an ordered list of weight values" with two specified properties (the SIMD-width vector of weights).
  - **Input activation map -> interleaved layout:** input pixels regrouped into **clusters**.
  - **Output -> interleaved output layout:** outputs produced tile by tile from the clusters.
- **In three-formula terms (analysis).**
  - F3 (weights): a runnel = your `Ohwi8o` inner block of 8 output channels; a kernel tile = the collection of runnels for a tap group.
  - F3 (activations/output): the interleaved layouts are channel-blocked formats like your `nChw8c`.
  - F2 is implied by "computed tile by tile from clusters": each output tile reads the cluster that is its input window.
- **Overlap with your kernel (technical note, not legal advice).** Your kernel's `Ohwi8o` weights (8 output channels packed innermost, in kernel read order) and `nChw8c` interleaved output are the same shapes this patent describes as "kernel runnels" and "interleaved layout". The difference: your choice is the oneDNN-documented, openly published `jit_avx2` branch (public prior art from MKL-DNN, 2016+), and you broadcast from plain `nchw` input rather than building clusters. If you ever claim or publish a *novel* packing method, read these independent claims first; for using the standard oneDNN layouts, the oneDNN source and docs are your prior-art reference.
- **Links:** [USPTO PDF 10489680](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/10489680) · [Justia 20180096226](https://patents.justia.com/patent/20180096226) · [Google Patents US11720800](https://patents.google.com/patent/US11720800) · [USPTO PDF 11182645](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/11182645) · [CN110073359A](https://patents.google.com/patent/CN110073359A/zh) · [JP2019535079A](https://patents.google.com/patent/JP2019535079A/ja)

---

## 4. Search log
- `US10489680B2 efficient data layouts for convolutional neural networks claims kernel tile layout runnel`
- `Magic Leap patent US10489680 "Efficient data layouts for convolutional neural networks" claims runnel tile kernel layout interleaved`
