# Apache Beam Data Engineering Exercise (Colab, Python)

**Author:** Prachi Gupta  
**Notebook:** `Apache_Beam.ipynb`  
**Tested environment:** Python 3.12.12 · Apache Beam 2.68.0 (DirectRunner, Colab)

## Overview
This project demonstrates the **core Apache Beam concepts** required by the assignment in a single, self-contained Google Colab notebook:
- **Pipeline I/O**: `ReadFromText` and `WriteToText`
- **Element-wise transforms**: **Map** and **Filter**
- **ParDo** with a custom **DoFn**
- **Composite transform** via a reusable `PTransform`
- **Partition** of a `PCollection` into multiple branches
- **Event-time windowing** using `FixedWindows` and per-window aggregation

We process a small synthetic dataset (`transactions.csv`) generated in-notebook, so the grader can run everything **without external data or credentials**.

## What the pipeline does
1. **Create dataset** (synthetic): `event_time, user_id, amount, category` with irregular time gaps for interesting windows.  
2. **I/O + Map/Filter**: read CSV → parse each line into a dict → drop invalid rows → write JSONL.  
3. **ParDo + Composite**: enrich records (amount tier, user bucket, high-value flag) inside a custom `DoFn` wrapped in a composite `PTransform`.  
4. **Partition**: split into **high_value** and **regular** streams and write each branch out.  
5. **Windowing**: attach event-time timestamps from `event_time`, apply **FixedWindows(60s)**, and compute **sum(amount)** per **category** per window.

## Repository structure
```
.
├── Apache_Beam.ipynb        # The Colab-ready notebook (primary deliverable)
├── VideoURL.txt
├── README.md                # This file
└── (Generated at runtime in Colab)
    └── /content/beam_demo/
        ├── data/            # transactions.csv (created in Cell 2)
        └── out/
            ├── 01_parsed/   # JSONL from Map/Filter
            ├── 02_enriched/ # JSONL after ParDo + composite transform
            ├── 03_partition/# JSONL for high_value and regular branches
            └── 04_windowed/ # JSONL with per-window aggregates
```

## How to run (Colab)
1. Open the notebook in Google Colab.  
2. Run **Cell 1 → Cell 7** in order (each prints a ✅ summary and a sample of outputs).  
3. Use the **Files** panel in Colab to browse `/content/beam_demo/out/...` and open the produced `.jsonl` files.

### Local run (optional)
- Install: `pip install apache-beam`  
- Launch Jupyter/Colab runtime and run the notebook top-to-bottom with the **DirectRunner** (default).

## Mapping to assignment requirements
| Requirement | Where in notebook |
|---|---|
| Pipeline I/O (read/write) | **Cell 3** (`ReadFromText`/`WriteToText`) |
| Map & Filter | **Cell 3** (`Map(parse_line)` / `Filter(is_valid)`) |
| ParDo (custom DoFn) | **Cell 4** (`EnrichDoFn` via `ParDo`) |
| Composite transform | **Cell 4** (`ParseValidateEnrich` `PTransform`) |
| Partition | **Cell 5** (`beam.Partition` → high_value vs regular) |
| Windowing | **Cell 6** (`TimestampedValue` + `FixedWindows(60s)` + `CombinePerKey`) |
| Outputs verification | **Cell 7** (artifact list + samples) |

## Inputs and outputs
- **Input:** `transactions.csv` (generated in **Cell 2**) with columns: `event_time, user_id, amount, category`.  
- **Intermediate/Final outputs:**  
  - `/out/01_parsed/transactions-*.jsonl`  
  - `/out/02_enriched/transactions_enriched-*.jsonl`  
  - `/out/03_partition/high_value-*.jsonl`, `/out/03_partition/regular-*.jsonl`  
  - `/out/04_windowed/windowed-*.jsonl`

Each output file contains one JSON object per line, making it easy to inspect and diff.

## References
- Apache Beam Programming Guide (Python)  
- Transforms: Map/Filter/ParDo, Windowing, Partition  
- Interactive/Colab examples from beam.apache.org

---
