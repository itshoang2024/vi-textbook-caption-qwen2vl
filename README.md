# Vietnamese Textbook Captioning with Qwen2-VL + LoRA

> **Goal:** finetune `Qwen/Qwen2-VL-2B-Instruct` to generate **narration-style, screen-reader friendly** Vietnamese captions for textbook pages (accessibility-first, supporting visually impaired learners).

---

## Links

[![Hugging Face (Adapter A)](https://img.shields.io/badge/HuggingFace-Adapter%20A-yellow?logo=huggingface)](https://huggingface.co/hoangphann/LoRA-Qwen2-VL-2B-Instruct-captioning)
[![Dataset](https://img.shields.io/badge/Dataset-bbdontcry%2Fvietnamese--image--captioning-blue?logo=huggingface)](https://huggingface.co/datasets/bbdontcry/vietnamese-image-captioning)

### Kaggle notebooks (final pipeline 01→05)

| # | Notebook | Link | Output | 
|---|---|---|---:|
| 1 | Finetune | [![Open in Kaggle](https://img.shields.io/badge/Open%20in%20Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/dangpham321/01-finetune-qwen-vl-config-a) | [`vn-textbook-qwen2vl-01-adapters`](https://www.kaggle.com/datasets/hoangphann/vn-textbook-qwen2vl-01-adapters) |
| 2 | Inference | [![Open in Kaggle](https://img.shields.io/badge/Open%20in%20Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/hoangphann/02-infer-qwen-vl) | [`vn-textbook-qwen2vl-02-predictions`](https://www.kaggle.com/datasets/hoangphann/vn-textbook-qwen2vl-02-predictions) |
| 3a | Metrics (Light) | [![Open in Kaggle](https://img.shields.io/badge/Open%20in%20Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/hoangphann/03a-qwen-vl-metrics-light) | [`vn-textbook-qwen2vl-03-metrics`](https://www.kaggle.com/datasets/hoangphann/vn-textbook-qwen2vl-03-metrics) |
| 3b | Metrics (Heavy) | [![Open in Kaggle](https://img.shields.io/badge/Open%20in%20Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/hoangphann/03b-qwen-vl-metrics-heavy) | [`vn-textbook-qwen2vl-03-metrics`](https://www.kaggle.com/datasets/hoangphann/vn-textbook-qwen2vl-03-metrics) |
| 4 | Merge results | [![Open in Kaggle](https://img.shields.io/badge/Open%20in%20Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/hoangphann/04-qwen-vl-merge-results) | [`vn-textbook-qwen2vl-04-results`](https://www.kaggle.com/datasets/hoangphann/vn-textbook-qwen2vl-04-results) |
| 5 | Demo (Gradio) | [![Open in Kaggle](https://img.shields.io/badge/Open%20in%20Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)](https://www.kaggle.com/code/hoangphann/05-qwen-vl-deploy) | - |

> Tip: Put these datasets as Kaggle inputs when running the corresponding notebooks.

---

## Highlights

- **Base model:** `Qwen/Qwen2-VL-2B-Instruct`
- **Finetuning:** LoRA / QLoRA (adapter-based)
- **Output style:** Vietnamese **narration-style detail caption** (suitable for screen readers)
- **Dataset:** [`bbdontcry/vietnamese-image-captioning`](https://huggingface.co/datasets/bbdontcry/vietnamese-image-captioning) (train/val/test)

---

## Naming convention (important)

This pipeline uses `RUN_ID` consistently across all notebooks. `RUN_ID` indicates **which model variant** the notebook targets. In this pipeline we define: `RUN_ID ∈ {baseline, A, B}`

Meaning:
- If `RUN_ID="baseline"`: the notebook loads the **base model**.
- If `RUN_ID="A"` or `RUN_ID="B"`: the notebook loads the **base model + corresponding LoRA adapter** (A or B).

### If `RUN_ID="baseline"`
- Base model: `Qwen/Qwen2-VL-2B-Instruct`

### If `RUN_ID="A"`
Adapter A is trained with the following hyperparameters:
  ```jsonc
  {
    'model_id': 'Qwen/Qwen2-VL-2B-Instruct',
    'quantization': None,
    'min_pixels': 200704,
    'max_pixels': 802816,
    'lora_r': 16,
    'lora_alpha': 32,
    'lora_dropout': 0.05,
    'lora_target': 'attn_only',
    'per_device_train_bs': 1,
    'per_device_eval_bs': 1,
    'grad_accum': 16,
    'learning_rate': 0.0002,
    'warmup_ratio': 0.03,
    'weight_decay': 0.0,
    'lr_scheduler': 'linear',
    'max_steps': 800,
    'eval_steps': 100,
    'save_steps': 100,
    'logging_steps': 10,
    'seed': 42,
    'gen_max_new_tokens_detail': 2048
  }
  ```

### If `RUN_ID="B"`
Adapter B is trained with the following hyperparameters:
  ```jsonc
  {
    'model_id': 'Qwen/Qwen2-VL-2B-Instruct',
    'quantization': None,
    'min_pixels': 200704,
    'max_pixels': 802816,
    'lora_r': 32,
    'lora_alpha': 64,
    'lora_dropout': 0.05,
    'lora_target': 'attn_mlp',
    'per_device_train_bs': 1,
    'per_device_eval_bs': 1,
    'grad_accum': 16,
    'learning_rate': 0.0001,
    'warmup_ratio': 0.03,
    'weight_decay': 0.0,
    'lr_scheduler': 'linear',
    'max_steps': 800,
    'eval_steps': 100,
    'save_steps': 100,
    'logging_steps': 10,
    'seed': 42,
    'gen_max_new_tokens_detail': 2048
  }
  ```

---

## Benchmark (test split)

Dataset: `bbdontcry/vietnamese-image-captioning` (test)

| Model | Quote-CER ↓ | Concept-Rec ↑ | LLM-Score ↑ | BERTScore ↑ | BLEU-4 ↑ | METEOR ↑ |
|---|---:|---:|---:|---:|---:|---:|
| Qwen2-VL-2B (Zero-shot) | 0.995 | 0.222 | 3.36/10 | 0.671 | 6.30 | 0.142 |
| **Qwen2-VL-2B + LoRA (RUN_ID=A)** | 0.385 | 0.632 | **5.09/10** | **0.837** | **43.73** | **0.521** |
| Qwen2-VL-2B + LoRA (RUN_ID=B) | **0.365** | **0.642** | 4.82/10 | 0.826 | 41.73 | 0.517 |

---
## Reproduce on Kaggle (recommended)

Since I already attached the Kaggle Inputs in each notebook, re-runners only need to open the notebooks in order **01 → 05** and click **Run all**.

### Step 1 - Finetune adapter A

- **Goal:** train a LoRA/QLoRA adapter.
- **Input:** HF dataset (train/val)
- **Config:** `RUN_ID="A"` or `RUN_ID="B"`
- **Output:**
  * `/kaggle/working/{RUN_ID}__Qwen2_VL_2B_Instruct/adapter/`
  * `/kaggle/working/{RUN_ID}__Qwen2_VL_2B_Instruct/processor/`
  * `/kaggle/working/{RUN_ID}__Qwen2_VL_2B_Instruct/run_config.json`

### Step 2 - Inference

- **Goal:** run inference on the test split for each `RUN_ID` (baseline/A/B).
- **Input:** HF dataset (test)
- **Output:**
  - `RUN_ID="baseline"` → outputs `baseline_predictions_test_detail.csv`
  - `RUN_ID="A"` → outputs `A_predictions_test_detail.csv`
  - `RUN_ID="B"` → outputs `B_predictions_test_detail.csv`

### Step 3 - Metrics

- **Goal:** compute metrics for each `RUN_ID`: Quote-CER, Concept-Rec, BLEU-4, METEOR, LLM-Score, BERTScore.
- **Input:** `{RUN_ID}_predictions_test_detail.csv`
- **Output:**
  * `{RUN_ID}_metrics_light.json`
  * `{RUN_ID}_metrics_heavy.json`

> 03b (Gemini judge) requires `GEMINI_API_KEY` in Kaggle Secrets.

### Step 4 - Merge results

- **Goal:** merge all metrics across `RUN_ID`s.
- **Input:** `{RUN_ID}_metrics_light.json` and `{RUN_ID}_metrics_heavy.json`
- **Output:**
  - `results.csv`
  - `results.json`

### Step 5 - Demo

- **Goal:** run a captioning demo with **Gradio**, supporting two modes:
- **Config:**
  - `RUN_KIND="baseline"`: run the base model
  - `RUN_KIND="adapter"`: run the base model + LoRA adapter
    - `ADAPTER_SOURCE="local"`: load the adapter from a Kaggle dataset
    - `ADAPTER_SOURCE="hf"`: load the adapter from Hugging Face
