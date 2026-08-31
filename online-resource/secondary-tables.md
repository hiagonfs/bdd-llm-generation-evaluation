# Secondary tables

These tables were moved from the main manuscript to keep the article focused on the study design and primary findings. Values and operational definitions are unchanged.

## OR-1. BDD-oriented criteria incorporated into the common prompt

| Criterion | Instruction incorporated into the prompt |
|---|---|
| Business outcome | Clearly express the business value or expected result represented by the scenario. |
| Single action and outcome | Keep the scenario focused on one principal action and its corresponding result. |
| Essential steps | Include only the `Given`, `When`, `Then`, and `And` steps required to express the behavior. |
| Declarative language | Describe the expected behavior without unnecessary implementation details or technical instructions. |
| Terminological consistency | Use consistent business terminology and avoid unnecessary technical jargon. |
| Scenario independence | Express the behavior so that the scenario can be understood independently from other scenarios. |
| Third-person perspective | Write behavioral steps in the third person to reduce ambiguity in their interpretation. |
| Conciseness | Avoid unnecessary repetition while retaining the information required to represent the behavior. |

## OR-2. Prompting conditions

| Strategy | In-context examples | Prompt composition |
|---|---:|---|
| Zero-shot | 0 | Common task instructions, BDD-oriented constraints, output format, Gherkin skeleton, and target test case. |
| One-shot | 1 | Zero-shot components plus one fixed input-output demonstration concerning an invalid-login scenario. |
| Few-shot | 3 | One-shot components plus two additional fixed demonstrations concerning password recovery and shopping-cart interaction. |

The [full prompt templates](prompts.md) provide the exact text.

## OR-3. Operationalization of the candidate automatic measures

| Measure | Operationalization | Preferred direction |
|---|---|---|
| BERTScore F1 | Generated Gherkin scenario compared with its expert reference using `bert-base-multilingual-cased` (layer 9), Portuguese configuration, baseline rescaling, and no IDF weighting. Precision, recall, and F1 were calculated; F1 was retained for metric validation. | Higher |
| METEOR | Expert Gherkin scenario used as the reference and generated scenario as the candidate. Both texts were lowercased and tokenized using NLTK, with lexical expansion based on WordNet before `single_meteor_score`. | Higher |
| Manhattan Distance | Each reference-generated pair was independently represented through term-frequency vectors using `CountVectorizer`, followed by L1 distance calculated with `cityblock`. | Lower |
| NLI-based score | Reference and generated Gherkin steps were grouped by behavioral role, aligned by role and position, and compared bidirectionally using a multilingual NLI model. Step-level values were aggregated into one scenario-level score. | Higher |

See [methodological details](methodological-details.md#automatic-metric-operationalization) for implementation signatures and equations.

## OR-4. Paired bootstrap comparisons of metric-human correlations

All comparisons use `n = 259`. Positive Δρ values indicate a stronger oriented Spearman association for BERTScore F1.

| Comparison | Δρ | 95% CI | Holm-adjusted p |
|---|---:|---:|---:|
| BERTScore vs. Manhattan | 0.1339 | [0.0482, 0.2189] | 0.0042 |
| BERTScore vs. METEOR | 0.3409 | [0.2140, 0.4712] | `<0.001` |
| BERTScore vs. NLI | 0.4477 | [0.3089, 0.5814] | `<0.001` |

The analysis used 10,000 paired bootstrap resamples. Two-sided p-values for all six metric pairs were adjusted jointly with the Holm procedure.

## OR-5. Pairwise prompting-strategy comparisons within each model

Difference is the paired mean BERTScore F1 of the first strategy minus the second. `r_rb` denotes rank-biserial correlation.

| Model | Comparison | Difference | 95% CI | r_rb | Holm-adjusted p |
|---|---|---:|---:|---:|---:|
| Qwen3-8B | Zero vs. One | 6.15 | [5.25, 7.06] | 0.807 | `<0.001` |
| Qwen3-8B | Zero vs. Few | 6.13 | [5.23, 7.08] | 0.796 | `<0.001` |
| Qwen3-8B | One vs. Few | -0.03 | [-0.57, 0.51] | 0.020 | 0.782 |
| Gemma-4-E4B-it | Zero vs. One | 2.89 | [2.11, 3.67] | 0.481 | `<0.001` |
| Gemma-4-E4B-it | Zero vs. Few | 4.43 | [3.57, 5.26] | 0.643 | `<0.001` |
| Gemma-4-E4B-it | One vs. Few | 1.54 | [0.81, 2.28] | 0.262 | `<0.001` |
| Granite-4.1-8B | Zero vs. One | 1.43 | [0.86, 1.98] | 0.373 | `<0.001` |
| Granite-4.1-8B | Zero vs. Few | 1.65 | [1.09, 2.25] | 0.406 | `<0.001` |
| Granite-4.1-8B | One vs. Few | 0.22 | [-0.10, 0.55] | 0.116 | 0.107 |
| Llama-3-8B | Zero vs. One | 0.89 | [0.27, 1.52] | 0.179 | 0.013 |
| Llama-3-8B | Zero vs. Few | 4.28 | [3.49, 5.07] | 0.692 | `<0.001` |
| Llama-3-8B | One vs. Few | 3.39 | [2.80, 3.98] | 0.749 | `<0.001` |
| Mistral-7B-v0.3 | Zero vs. One | 7.73 | [6.85, 8.67] | 0.916 | `<0.001` |
| Mistral-7B-v0.3 | Zero vs. Few | 7.51 | [6.60, 8.42] | 0.880 | `<0.001` |
| Mistral-7B-v0.3 | One vs. Few | -0.23 | [-0.90, 0.43] | 0.120 | 0.095 |

## OR-6. Pairwise model comparisons under zero-shot prompting

Difference is the paired mean BERTScore F1 of the first model minus the second.

| Comparison | Difference | 95% CI | r_rb | Holm-adjusted p |
|---|---:|---:|---:|---:|
| Qwen – Gemma | 11.83 | [10.82, 12.81] | 0.960 | `<0.001` |
| Qwen – Granite | 5.48 | [4.39, 6.57] | 0.602 | `<0.001` |
| Qwen – Llama | 0.23 | [-0.70, 1.14] | 0.035 | 0.625 |
| Qwen – Mistral | 4.01 | [2.95, 5.10] | 0.472 | `<0.001` |
| Granite – Gemma | 6.35 | [5.35, 7.37] | 0.713 | `<0.001` |
| Llama – Gemma | 11.60 | [10.65, 12.52] | 0.958 | `<0.001` |
| Mistral – Gemma | 7.82 | [6.87, 8.78] | 0.843 | `<0.001` |
| Llama – Granite | 5.25 | [4.18, 6.34] | 0.573 | `<0.001` |
| Mistral – Granite | 1.47 | [0.51, 2.40] | 0.254 | `<0.001` |
| Llama – Mistral | 3.78 | [2.81, 4.71] | 0.494 | `<0.001` |

## OR-7. Within-case standard deviation of BERTScore F1

Each cell reports the mean followed by the median in parentheses across the 259 source cases.

| Model | Zero-shot | One-shot | Few-shot |
|---|---:|---:|---:|
| Qwen3-8B | 2.68 (2.15) | 1.79 (1.34) | 1.71 (1.41) |
| Gemma-4-E4B-it | 9.43 (6.66) | 8.82 (6.66) | 10.33 (10.04) |
| Granite-4.1-8B | 3.63 (3.17) | 2.74 (2.41) | 2.38 (2.10) |
| Llama-3-8B-Instruct | 4.86 (4.42) | 3.57 (3.40) | 3.44 (3.20) |
| Mistral-7B-Instruct-v0.3 | 5.41 (4.74) | 4.64 (3.51) | 4.02 (3.16) |
