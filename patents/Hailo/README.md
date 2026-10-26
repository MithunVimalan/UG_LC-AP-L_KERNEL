# Hailo Technologies

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = confirmed; `P` = inferred.

---

## 1. Why Hailo matters for your kernel

Hailo (Hailo-8 / Hailo-10 edge NPUs) uses a **structure-defined dataflow**: the compiler maps each layer onto dedicated compute+memory resources, so there is no fixed external tensor format exposed — its native layout `NHCW` is named only in community docs. The patent found is about the **processing element that fuses compute and local memory** (F1/F3 co-location), not about a layout formula.

---

## 2. Summary table

| # | Number | Title | F | Status | V |
|---|---|---|---|---|---|
| 1 | US11514291B2 | Neural network processing element incorporating compute and local memory elements | F1 + F3 | granted | V |

## 3. Patent details

### 3.1 US11514291B2 — NN processing element incorporating compute and local memory
- **Assignee:** Hailo Technologies Ltd. A processing element that **co-locates compute and local memory**, so weights/activations for a layer sit next to the MACs that use them (the resource-mapped dataflow).
- **In three-formula terms (analysis).** F1 + F3 fused: the layout *is* the placement of each layer's data in the PE that computes it; the schedule is the static resource map. This is the "layout as planner output" camp (like Graphcore).
- **Links:** [Google Patents US11514291](https://patents.google.com/patent/US11514291)

## 4. Public (non-patent) material
- `NHCW` named as the native HW format (community, not Hailo staff): [Hailo Community](https://community.hailo.ai/t/can-i-compile-a-hef-with-nhcw-native-input-layout-to-skip-the-nhwc-nhcw-conversion/19501)
- Dataflow compiler decomposes each layer into computational elements: [Hailo style guide](https://hailo.ai/style-guide)

## 5. Searched and not found
- No Hailo patent found that fixes the `NHCW` layout or a convolution access map.

## 6. Search log
- `Hailo Technologies patent OR Rockchip patent neural network accelerator data layout convolution dataflow structured US assignee`
