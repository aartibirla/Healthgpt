# HealthGPT-Personal

**HealthGPT-Personal for Multimodal Patient-Specific Clinical Decision Support Using Personalised Large Language Models**

Aarti¹, Devineni Gireesh Kumar², Akella S Narasimha Raju³, Reem A. Almenweer⁴

---

## Overview

HealthGPT-Personal is a multimodal, patient-conditioned LLM framework for personalised clinical decision support. It integrates:

- **Personal Patient Embedding (PPE)** — fuses demographics, EHR codes, lab trends, clinical notes, and imaging features via cross-modal attention
- **P-LoRA** — shared low-rank adapters (rank=32, α=64) at every attention projection layer, combined with patient-conditioned attention bias g(p)
- **Hybrid retrieval** — RAG + UMLS knowledge-graph traversal personalised by patient state
- **Medical Alignment and Risk Filter** — hallucination-risk classifier + rule-based medication contraindication checking

---

## Data Availability

### Datasets used in this study

| Dataset | Access | Notes |
|---|---|---|
| **MIMIC-IV v2.2** | https://physionet.org/content/mimiciv/2.2/ | Requires PhysioNet credentialed access + CITI training + DUA. Restricted. |
| **n2c2 (2014–2018)** | https://portal.dbmi.hms.harvard.edu/ | Requires Harvard DBMI DUA. Restricted. 
| **MedQA (USMLE)** | https://github.com/jind11/MedQA | Public. No DUA required. |
| **MedMCQA** | https://medmcqa.github.io/ | Public. No DUA required. |
| **PubMedQA** | https://pubmedqa.github.io/ | Public. No DUA required. |
| **HealthSearchQA** | Singhal et al., *Nature* 2023 supplementary | See original Med-PaLM paper for access instructions. |

> **Important:** MIMIC-IV and n2c2 are governed by Data Use Agreements. Raw data cannot be shared in this repository. You must apply for and obtain your own credentialed access through the links above.

### Baseline results on public datasets (reproducible without DUA)

We provide independently reproducible baseline results on the publicly available MultiMedQA subsets using the script `scripts/06_public_qa_baseline.py`:

| Dataset | Model | n | Accuracy |
|---|---|---|---|
| MedQA | flan-t5-small (zero-shot) | 100 | 21.0% |
| PubMedQA | flan-t5-small (zero-shot) | 100 | 44.0% |
| MedMCQA | flan-t5-small (zero-shot) | 100 | 25.0% |

These serve as a publicly reproducible baseline. Results for the full HealthGPT-Personal system require MIMIC-IV and n2c2 access.

---

## Installation

```bash
git clone https://github.com/YOUR_USERNAME/healthgpt-personal.git
cd healthgpt-personal
pip install -r requirements.txt
```

---

## Repository Structure

```
healthgpt-personal/
├── healthgpt/
│   ├── ppe.py                  # Personal Patient Embedding (Section 3.2)
│   ├── plora.py                # P-LoRA + patient-conditioned attention (Section 3.3)
│   ├── retrieval.py            # Hybrid RAG + KG retrieval (Section 3.3)
│   ├── safety.py               # Medical Alignment and Risk Filter (Section 3.4)
│   ├── model.py                # Full HealthGPT-Personal model
│   └── preprocessing.py        # Multimodal preprocessing (Section 4.2)
├── scripts/
│   ├── 06_public_qa_baseline.py  # Run on public datasets (no DUA needed)
│   ├── 07_patient_conditioned_pipeline.py  # Run on MIMIC-IV (DUA required)
│   ├── 01_per_task_breakdown.py
│   ├── 02_bootstrap_stats.py
│   └── 04_recompute_headline_metrics.py
├── configs/
│   └── default_config.yaml
├── tests/
│   └── test_components.py
├── requirements.txt
└── README.md
```

---

## Quick Start (public datasets, no DUA needed)

```bash
pip install transformers torch datasets pandas tqdm
python scripts/06_public_qa_baseline.py --dataset medqa --n-examples 100 --output medqa_results.csv
python scripts/06_public_qa_baseline.py --dataset pubmedqa --n-examples 100 --output pubmedqa_results.csv
python scripts/06_public_qa_baseline.py --dataset medmcqa --n-examples 100 --output medmcqa_results.csv
```

## Full Pipeline (requires MIMIC-IV access)

```bash
# After downloading MIMIC-IV locally:
python scripts/07_patient_conditioned_pipeline.py \
    --input your_mimic_task_data.csv \
    --output personalization_results.csv
```

---

## Citation

If you use this code, please cite:

```
Aarti, Kumar DG, Raju ASN, Almenweer RA. HealthGPT-Personal for Multimodal
Patient-Specific Clinical Decision Support Using Personalised Large Language
Models. [Journal], 2026.
```

---

## License

MIT License. Note: The datasets this code was evaluated on (MIMIC-IV, n2c2) carry their own separate Data Use Agreements and may not be redistributed.
