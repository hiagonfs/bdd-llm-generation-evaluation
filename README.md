# BDD LLM Generation Evaluation

Reproducibility package for the study reported in:

**“Evaluating LLMs and Prompting Strategies for BDD Scenario Generation: Reference-Based Performance and Repeated-Run Stability.”**

The study evaluates five open-weight language models and three prompting strategies for generating one Portuguese BDD scenario in Gherkin syntax from each natural-language software test case.

## Reproducibility materials

The [online-resource/](online-resource/) directory provides detailed methodological documentation and study artifacts that support independent inspection and reproduction:

- [full zero-shot, one-shot, and few-shot prompt templates](online-resource/prompts.md);
- [metric implementations, operational equations, and convergence derivation](online-resource/methodological-details.md);
- [secondary methodological and inferential tables](online-resource/secondary-tables.md).

## Experimental design

- 259 source test cases;
- 5 models;
- 3 prompting strategies: zero-shot, one-shot, and few-shot;
- 10 executions per case-model-prompt combination;
- 38,850 generated BDD scenarios.

The same finalized prompt templates were applied across all models. One-shot used one fixed demonstration, and few-shot used three fixed demonstrations.

## Evaluated models

| Model | Checkpoint |
|---|---|
| Qwen3-8B | `Qwen/Qwen3-8B` |
| Gemma 4 E4B IT | `google/gemma-4-E4B-it` |
| Granite 4.1 8B | `ibm-granite/granite-4.1-8b` |
| Llama 3 8B Instruct | `meta-llama/Meta-Llama-3-8B-Instruct` |
| Mistral 7B Instruct v0.3 | `mistralai/Mistral-7B-Instruct-v0.3` |

## Execution environment

The scenario-generation experiments were executed in a self-hosted RunPod environment with the following configuration:

- Python 3.12.3
- PyTorch 2.8.0+cu128
- CUDA 12.8
- NVIDIA A40 GPU
- 44.43 GiB GPU memory
- NVIDIA driver 570.211.01

The package versions used for scenario generation are pinned in:

[`requirements-generation.txt`](requirements-generation.txt)

To install the generation dependencies:

```bash
pip install -r requirements-generation.txt
```

## Repository contents

| Path | Contents |
|---|---|
| [dataset/](dataset/) | Experimental sample and source test cases. |
| [reference base/](reference%20base/) | Expert-validated Gherkin reference scenarios. |
| [prompts/](prompts/) | Model-specific generation notebooks containing the exact prompts and execution logic. |
| [generations/](generations/) | Generated scenarios organized by model and prompting strategy. |
| [metrics/](metrics/) | Automatic metric outputs. |
| [human evaluation/](human%20evaluation/) | Blinded human-evaluation data and associated artifacts. |
| [scripts/](scripts/) | Metric calculation and repeated-run convergence notebooks. |
| [statistical-analysis/](statistical-analysis/) | Metric-validation and comparative statistical-analysis notebooks. |
| [online-resource/](online-resource/) | Reproducibility documentation for prompts, metric implementations, convergence procedures, and detailed tables. |

## Evaluation

The study calculates four reference-based measures:

- BERTScore F1;
- METEOR;
- Manhattan Distance;
- a bidirectional step-level NLI score.

A human assessment of 259 sampled generations evaluates Structure, Semantics, and Details. Repeated-generation analyses separately examine textual convergence with Normalized Token-Level Levenshtein Distance and dispersion of reference-based performance across runs.

## Reproduction notes

The executable Jupyter notebooks record model-specific chat-template handling, generation parameters, output validation, metric implementation, and statistical procedures. Paths may need to be adapted to the local or RunPod environment before execution. The [methodological details](online-resource/methodological-details.md) page maps each documented operation to its notebook.

## License

This repository is released under the [MIT License](LICENSE).
