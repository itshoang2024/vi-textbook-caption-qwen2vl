# Vietnamese Textbook Narration Captioning (SGK) with Qwen2-VL + LoRA

> **Goal:** finetune `Qwen/Qwen2-VL-2B-Instruct` to generate **narration-style, screen-reader friendly** Vietnamese captions for textbook pages (accessibility-first, supporting visually impaired learners).

---

## Links

[![Hugging Face (Adapter A)](https://img.shields.io/badge/HuggingFace-Adapter%20A-yellow?logo=huggingface)](https://huggingface.co/hoangphann/LoRA-Qwen2-VL-2B-Instruct-captioning)
[![Dataset](https://img.shields.io/badge/Dataset-bbdontcry%2Fvietnamese--image--captioning-blue?logo=huggingface)](https://huggingface.co/datasets/bbdontcry/vietnamese-image-captioning)

### Kaggle notebooks (final pipeline 01→05)

- [![Open in Kaggle](https://img.shields.io/badge/Open%20in%20Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/dangpham321/01-finetune-qwen-vl-config-a) **01 - Finetune**
- [![Open in Kaggle](https://img.shields.io/badge/Open%20in%20Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/hoangphann/02-infer-qwen-vl) **02 - Inference**
- [![Open in Kaggle](https://img.shields.io/badge/Open%20in%20Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/hoangphann/03a-qwen-vl-metrics-light) **03A - Metrics (Light):** Quote-CER, Concept-Rec, BLEU-4, METEOR
- [![Open in Kaggle](https://img.shields.io/badge/Open%20in%20Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/hoangphann/03b-qwen-vl-metrics-heavy) **03B - Metrics (Heavy):** BERTScore, LLM-Score
- [![Open in Kaggle](https://img.shields.io/badge/Open%20in%20Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/hoangphann/04-qwen-vl-merge-results) **04 - Merge results**
- [![Open in Kaggle](https://img.shields.io/badge/Open%20in%20Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/hoangphann/05-qwen-vl-deploy) **05 - Demo (Gradio)**

### Kaggle datasets (outputs / artifacts)

These are the stage outputs used across notebooks:

- [`vn-textbook-qwen2vl-01-adapters`](https://www.kaggle.com/datasets/hoangphann/vn-textbook-qwen2vl-01-adapters) (LoRA adapters)
- [`vn-textbook-qwen2vl-02-predictions`](https://www.kaggle.com/datasets/hoangphann/vn-textbook-qwen2vl-02-predictions) (test predictions)
- [`vn-textbook-qwen2vl-03-metrics`](https://www.kaggle.com/datasets/hoangphann/vn-textbook-qwen2vl-03-metrics) (light/heavy metrics)
- [`vn-textbook-qwen2vl-04-results`](https://www.kaggle.com/datasets/hoangphann/vn-textbook-qwen2vl-04-results) (merged results)

> Tip: Put these datasets as Kaggle inputs when running the corresponding notebooks.

---

## Highlights

- **Base model:** `Qwen/Qwen2-VL-2B-Instruct`
- **Finetuning:** LoRA / QLoRA (adapter-based)
- **Output style:** Vietnamese **narration-style detail caption** (suitable for screen readers)
- **Dataset:** `bbdontcry/vietnamese-image-captioning` (train/val/test)

---

## Naming convention (important)

We use a strict naming convention across all notebooks:

- **RUN_ID**: run identifier + **file prefix**  
  `RUN_ID ∈ {baseline, A, B, demo}`  
  All artifacts follow: `{RUN_ID}_...`  
  e.g., `A_predictions_test_detail.csv`, `baseline_metrics_light.json`, ...

- **RUN_KIND**: inference mode in notebook 02  
  `RUN_KIND ∈ {baseline, adapter}`  
  If `RUN_KIND="baseline"` → `RUN_ID="baseline"`  
  If `RUN_KIND="adapter"` → `RUN_ID` is read from `run_config.json`

---

## Benchmark (test split)

Dataset: `bbdontcry/vietnamese-image-captioning` (test)

| Model | Quote-CER ↓ | Concept-Rec ↑ | LLM-Score ↑ | BERTScore ↑ | BLEU-4 ↑ | METEOR ↑ |
|---|---:|---:|---:|---:|---:|---:|
| Qwen2-VL-2B (Zero-shot) | 0.995 | 0.222 | 3.36/10 | 0.671 | 6.30 | 0.142 |
| **Qwen2-VL-2B + LoRA (RUN_ID=A)** | **0.385** | **0.632** | **5.09/10** | **0.837** | **43.73** | **0.521** |

---

## Reproduce on Kaggle (recommended)

### Step 0 - Add Kaggle inputs

* Dataset: `bbdontcry/vietnamese-image-captioning`
* (Optional) Stage datasets as inputs:

  * `vn-textbook-qwen2vl-01-adapters`
  * `vn-textbook-qwen2vl-02-predictions`
  * `vn-textbook-qwen2vl-03-metrics`
  * `vn-textbook-qwen2vl-04-results`

### Step 1 - Finetune adapter A

Run **01** with:

* `RUN_ID="A"`

Output:

* `A__Qwen2_VL_2B_Instruct/` (adapter + run_config)

### Step 2 - Inference

Run **02**:

* baseline: `RUN_KIND="baseline"` → outputs `baseline_*`
* adapter A: `RUN_KIND="adapter"` + point to `A__Qwen2_VL_2B_Instruct/` → outputs `A_*`

Output:

* `A_predictions_test_detail.csv`, `A_inference_config.json`
* `baseline_predictions_test_detail.csv`, ...

### Step 3 - Metrics

Run **03a** (light) and **03b** (heavy) using:

* `{RUN_ID}_predictions_test_detail.csv`

Output:

* `{RUN_ID}_metrics_light.json`
* `{RUN_ID}_metrics_heavy.json`

> 03b (Gemini judge) may require `GEMINI_API_KEY` in Kaggle Secrets.

### Step 4 - Merge results

Run **04** to merge light+heavy across run_ids:

* `results.csv`
* `results.json`

### Step 5 - Demo

Run **05** (Gradio) to demo caption generation interactively.