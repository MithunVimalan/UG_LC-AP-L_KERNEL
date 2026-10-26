# Etched (Etched.ai Inc.)

Patents on the three formulas (F1 schedule, F2 access map, F3 layout). Legend and method: see [`../README.md`](../README.md). `V` = number, title, assignee confirmed; `P` = assignee inferred.

---

## 1. Why Etched matters (and where it does not)

Etched (San Jose; founded 2022 by Gavin Uberti, Chris Zhu, Robert Wachen) builds **Sohu**, an ASIC built around transformer inference (TSMC 4 nm-class, HBM3E) ([Wikipedia](https://en.wikipedia.org/wiki/Etched_(company))). It is a young filer: the visible portfolio is a handful of 2023-2024 applications and at least one 2025 grant. Everything found is about **systolic arrays, attention execution and model-specific compilation** — F1. **No Etched patent touches convolution access maps (F2) or tensor storage layouts (F3).** For a conv kernel like yours, Etched is not a layout competitor.

Third-party analysis of Sohu and its filings: [Peak FLOPS, "breaking down Etched's Sohu"](https://wafer.substack.com/p/breaking-down-etcheds-sohu), [Peak FLOPS, "a whole new Etched"](https://wafer.substack.com/p/a-whole-new-etched).

---

## 2. Summary table

| # | Number | Title | F | Filed / published | Status | V |
|---|---|---|---|---|---|---|
| 1 | (granted 15 Jul 2025; number not captured) | Model-specific ASIC compilation by modifying template models | F1 | filed 22 Oct 2024 | granted | V (assignee ETCHED.AI INC per Justia inventor page) |
| 2 | US20240378175A1 | Multi-chip systolic arrays | F1 | pub. 2024 | application | P |
| 3 | US20240419516A1 | Parallel execution of self-attention-based AI models | F1 | pub. 2024 | application | P |
| 4 | US20250138820A1 | (compiler) | F1 | pub. 2025 | application | P |
| 5 | two compiler applications (inventors Tom Shlomi, Gavin Uberti) | IR -> executable code for a second hardware platform | F1 | filed Oct/Nov 2023; one pub. 15 May 2025 | applications | P |

Numbers 2-4 are named by the Peak FLOPS analysis and the Justia inventor page; the assignee of 2-4 was not shown in the snippets.

---

## 3. Details

### 3.1 Model-specific ASIC compilation (granted 15 Jul 2025)
- **Assignee:** ETCHED.AI INC. **Inventor:** Gavin Uberti. Filed 22 Oct 2024.
- **What it covers.** The compiler uses **structural parameters** of a model to **modify compilation data of a base (template) AI model**, so a new model of the same family compiles by editing a template rather than from scratch.
- **Link:** [Justia inventor page, Gavin Uberti](https://patents.justia.com/inventor/gavin-uberti)

### 3.2 US20240378175A1 — Multi-chip systolic arrays
- **What it covers.** A **combined systolic array** formed by interconnecting several ICs, each with a local systolic array, using chip-to-chip links that couple the local arrays.
- **In three-formula terms (analysis).** F1 across chips: the GEMM schedule of one large systolic array is cut into per-chip sub-arrays.
- **Link:** [Justia 20240378175](https://patents.justia.com/patent/20240378175)

### 3.3 US20240419516A1 — Parallel execution of self-attention-based AI models
- **What it covers.** An AI hardware platform with at least one IC containing **a systolic array and a self-attention circuit** (two parallel compute blocks).
- **Link:** [Justia inventor page](https://patents.justia.com/inventor/gavin-uberti)

---

## 4. Search log
- `Etched.ai patent transformer ASIC Sohu patent application assignee Etched`
- `"Etched" Inc patent application transformer inference hardware attention matrix systolic`
- `"Etched.ai" OR "Etched, Inc." patent USPTO filed assignee transformer chip`
- `US20240378175A1 "Multi-Chip Systolic Arrays" Etched claims`
- `US20240419516A1 "Parallel execution of self-attention-based AI models" Etched`
- `Etched.ai patent "model-specific" ASIC compilation template models granted July 2025 Gavin Uberti patent number`
- `"Etched.ai" assignee patent "systolic" OR "attention" OR "compil" justia Uberti Shlomi`

Note: applications publish about 18 months after filing, so 2025-2026 Etched filings may not be visible yet.
