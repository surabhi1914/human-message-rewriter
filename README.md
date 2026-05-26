# Human Message Rewriter

![Project Status](https://img.shields.io/badge/status-notebook%20prototype-10b981)
![Documentation](https://img.shields.io/badge/docs-in%20progress-2563eb)
![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white)
![Hugging Face](https://img.shields.io/badge/Hugging%20Face-Transformers-FFD21E?logo=huggingface&logoColor=black)
![Datasets](https://img.shields.io/badge/Hugging%20Face-Datasets-FFD21E?logo=huggingface&logoColor=black)
![PEFT](https://img.shields.io/badge/PEFT-parameter--efficient%20fine--tuning-8b5cf6)
![TRL](https://img.shields.io/badge/TRL-training%20LLMs-06b6d4)
![LoRA](https://img.shields.io/badge/LoRA%20%2F%20QLoRA-fine--tuning-10b981)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)

A Python notebook project for rewriting messages so they sound clearer, more
natural, and more human while preserving the original meaning.

The current work is focused on dataset preparation and baseline model
evaluation before fine-tuning.

## Overview

Human Message Rewriter is intended to help turn rough drafts, terse notes, and
awkward messages into clearer communication. The goal is not to erase the
writer's voice, but to improve tone, readability, and flow while keeping the
message useful and authentic.

## Current Status

This repository is now a notebook-based prototype, not just a planning outline.

Completed so far:

- Built a raw JSONL dataset with 300 curated rewrite examples
- Validated dataset structure, required fields, duplicate IDs, empty fields, and
  suspicious rewrite lengths
- Split the dataset into train, validation, and test sets
- Ran baseline inference on the held-out test set with
  `Qwen/Qwen2.5-0.5B-Instruct`
- Saved baseline outputs for later comparison against a fine-tuned model

Not included yet:

- Fine-tuning notebook
- Trained adapter or model checkpoint
- Production API
- User interface
- Deployment configuration

## Dataset

The raw dataset lives at:

```text
data/raw/human_rewriter_raw.jsonl
```

Each JSONL row follows this schema:

```json
{
  "id": "ex_0001",
  "category": "workplace",
  "instruction": "Rewrite this message so it sounds natural, clear, and human while keeping the same meaning.",
  "input": "Original message text",
  "output": "Human rewritten message text",
  "source": "synthetic_curated"
}
```

Current dataset size:

| Split      | Rows | File                                |
| ---------- | ---: | ----------------------------------- |
| Raw        |  300 | `data/raw/human_rewriter_raw.jsonl` |
| Train      |  240 | `data/processed/train.jsonl`        |
| Validation |   30 | `data/processed/validation.jsonl`   |
| Test       |   30 | `data/processed/test.jsonl`         |

The dataset covers these categories:

- `awkward_casual`
- `clearer_text`
- `follow_up`
- `formal_email`
- `polite_request`
- `reminder`
- `student_message`
- `wordy_to_concise`
- `workplace`

## Notebook Workflow

Run the notebooks in order:

1. `notebooks/01_validate_dataset.ipynb`

   Validates the raw dataset. It checks file loading, required fields, empty
   values, duplicate IDs, category distribution, identical input/output pairs,
   and suspiciously short or long outputs.

2. `notebooks/02_split_dataset.ipynb`

   Creates the processed train, validation, and test JSONL files using an
   80/10/10 split. The notebook also checks that every split contains examples
   from every category.

3. `notebooks/03_baseline_inference.ipynb`

   Loads the test set, runs the base model, cleans generated text, saves
   baseline outputs, and creates a baseline quality review.

The notebooks use paths such as `../data/...`, so keep the existing project
layout when running them.

## Baseline Model

The baseline inference notebook uses:

```text
Qwen/Qwen2.5-0.5B-Instruct
```

Generation currently runs on CPU by default:

```python
device = "cpu"
```

The first baseline run downloads the model from Hugging Face, so it requires
internet access and can take time depending on the machine.

Generated baseline files:

```text
outputs/baseline_outputs.jsonl
outputs/baseline_preview.csv
outputs/baseline_quality_review.csv
```

These files are generated artifacts and are ignored by git.

Current baseline review summary:

| Metric                                 |       Value |
| -------------------------------------- | ----------: |
| Test examples evaluated                |          30 |
| Empty cleaned outputs                  |           0 |
| Cleaned outputs same as input          |           0 |
| Very short cleaned outputs             |           0 |
| Raw outputs with extra generated text  |          30 |
| Average input length                   | 12.83 words |
| Average expected output length         |  9.60 words |
| Average cleaned baseline output length | 13.60 words |

The raw baseline generations often include extra text after the rewrite. The
notebook keeps both `raw_baseline_output` for traceability and
`baseline_output` for cleaned review.

## Tech Stack

This project is planned around a Python-based language model workflow for data
preparation, fine-tuning, and evaluation.

| Area              | Technology                |
| ----------------- | ------------------------- |
| Language          | Python                    |
| Model tooling     | Hugging Face Transformers |
| Dataset tooling   | Hugging Face Datasets     |
| Fine-tuning       | PEFT                      |
| Training workflow | TRL                       |
| Adapter methods   | LoRA / QLoRA              |
| Experimentation   | Jupyter Notebook          |

## Setup

Create and activate a virtual environment:

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

Install dependencies:

```powershell
pip install -r requirements.txt
```

Optional: register the environment as a Jupyter kernel:

```powershell
python -m ipykernel install --user --name human-message-rewriter --display-name "Python (human-message-rewriter)"
```

Start Jupyter:

```powershell
jupyter notebook
```

Then open the notebooks in the `notebooks/` folder and run them in order.

## Project Structure

```text
Human Message Rewriter/
+-- README.md
+-- requirements.txt
+-- data/
|   +-- raw/
|   |   +-- human_rewriter_raw.jsonl
|   +-- processed/
|       +-- train.jsonl
|       +-- validation.jsonl
|       +-- test.jsonl
+-- notebooks/
|   +-- 01_validate_dataset.ipynb
|   +-- 02_split_dataset.ipynb
|   +-- 03_baseline_inference.ipynb
+-- outputs/
    +-- baseline_outputs.jsonl
    +-- baseline_preview.csv
    +-- baseline_quality_review.csv
```

Notes:

- `data/raw/` contains the source dataset.
- `data/processed/` is generated by the split notebook.
- `outputs/` is generated by the baseline inference notebook.
- Model files, adapters, checkpoints, and generated outputs are ignored by git.

## Requirements

The project currently uses:

- `torch`
- `transformers`
- `datasets`
- `accelerate`
- `pandas`
- `tqdm`
- `jupyter`
- `ipykernel`
- `scikit-learn`

## Next Steps

- Adding a fine-tuning notebook using the processed train and validation splits
- Comparing fine-tuned outputs against the saved baseline outputs
- Adding quantitative and manual evaluation notes
- Saving adapter checkpoints under an ignored model output directory
- Adding a simple inference script or app after the fine-tuning workflow is stable

## Contributing

Ideas, issue reports, and improvements are welcome. Since the project is still
early, the most useful contributions are:

- Clear rewrite examples
- Tone and style suggestions
- Product feedback
- Implementation ideas

## License

No license has been added yet.
