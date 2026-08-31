# Methodological details

This Online Resource preserves implementation-level detail removed from the main manuscript for concision. The notation follows the manuscript.

## Experimental cardinalities and units

For source case `c`, model `m`, prompting strategy `p`, and execution `r`, the generated scenario is denoted by `G[c,m,p,r]`, and the expert-validated Gherkin reference for case `c` by `R[c]`.

The complete experiment produced:

```text
259 cases × 5 models × 3 prompting strategies × 10 executions
= 38,850 generated scenarios
```

Each automatic metric therefore contained 38,850 scenario-level measurements. The inferential comparisons of models and prompting strategies aggregated the ten executions for each case-model-prompt combination:

```text
259 cases × 5 models × 3 prompting strategies
= 3,885 case-level experimental observations
```

Repeated executions were not treated as independent observations.

## Dataset sampling and generation notation

For a finite eligible population, the target sample size was calculated as:

```text
n = [N × z² × p(1-p)] / [e²(N-1) + z² × p(1-p)]
```

Here, `N` is the eligible population size, `z` the critical value for the confidence level, `p` the assumed population proportion, and `e` the maximum margin of error. With `N = 787`, `z = 1.96`, `p = 0.50`, and `e = 0.05`, the estimate was 258.37 and was rounded up to 259.

Proportionate allocation to project stratum `h` used:

```text
n_h = (N_h / N) × n
```

Integer parts were assigned first, and remaining cases were distributed by largest fractional remainders. Sampling within projects and the final shuffle used random state 42.

For notation, the output produced from test case `T_c` by model `m`, prompting strategy `p`, and execution `r` was:

```text
G[c,m,p,r] = f[m,p](T_c; r)
```

All configurations used the same `T_c` and corresponding expert reference `R_c` for a given case.

## Automatic metric operationalization

All reference-generated pairs were joined by case identifier, and each score retained the unique generation identifier used to join the automatic and human-evaluation records.

### BERTScore F1

BERTScore used generated scenarios as candidates and expert Gherkin scenarios as references. The configuration was:

- encoder: `bert-base-multilingual-cased`;
- layer: 9;
- configured language: Portuguese (`pt`);
- baseline rescaling: enabled;
- inverse-document-frequency weighting: disabled;
- tokenizer: slow implementation;
- retained component: F1;
- reporting scale: multiplied by 100.

The evaluation environment recorded Python 3.13.15, PyTorch 2.11.0+cu128, Transformers 5.15.1, and the installed `bert-score` package version 0.3.13. The signature returned by the implementation was:

```text
bert-base-multilingual-cased_L9_no-idf_version=0.3.12 (hug_trans=5.15.1)-rescaled
```

The difference between the installed package version and the version encoded in the returned signature is reported exactly as recorded by the implementation.

Executable source: [BERTScore notebook](../scripts/Metrica_BERTScore_JSON_Ground_Truth_Geracoes_Adaptado_v2.ipynb).

### METEOR

The expert Gherkin scenario was the reference and the generated scenario the candidate. Both texts were lowercased and tokenized with NLTK. Tokens were expanded with lemma names obtained through WordNet before `single_meteor_score` was computed. The result was rounded to two decimal places.

### Manhattan Distance

Each reference-generated pair was independently represented through term-frequency vectors over the vocabulary occurring in that pair using `CountVectorizer`. Manhattan Distance was then calculated as the L1 distance with `cityblock`. Lower values indicate closer lexical distributions; the measure does not directly encode Gherkin structure or behavioral correctness.

Executable source: [Manhattan Distance notebook](../scripts/Distancia_Manhattan_JSON_Ground_Truth_Geracoes_Adaptado_v2.ipynb).

### NLI-based score

Gherkin steps were grouped by the behavioral roles `Given`, `When`, and `Then`; an `And` step inherited the role of the preceding keyword. Reference and generated steps were aligned by role and ordinal position. The multilingual `MoritzLaurer/mDeBERTa-v3-base-mnli-xnli` model estimated entailment, neutrality, and contradiction probabilities.

For one inference direction, an aligned step pair received:

```text
s = p_E + 0.5 × p_N
```

Here, `p_E` and `p_N` are the probabilities of entailment and neutrality. Each aligned pair was evaluated in both directions. The two directional scores were averaged, and the scenario-level score was the mean of the bidirectional aligned-step scores. Coverage—the proportion of reference steps for which an aligned generated step was available—was retained as diagnostic information and was not incorporated into the metric-selection score.

Executable source: [NLI notebook](../scripts/Metrica_NLI_Por_Passos_Bidirecional_Optimizado_Corrigido_v5.ipynb).

## Repeated generation and convergence derivation

Convergence was evaluated separately for each of the 15 model-prompting configurations using the ten generations of each of the 259 source cases. It measures within-configuration textual variability and does not use the expert reference.

Before comparison, generated scenarios underwent Unicode NFKC normalization, lowercasing, and tokenization with punctuation preserved as individual tokens.

For two generated scenarios `A` and `B`, with token sequences `T(A)` and `T(B)`, Normalized Token-Level Levenshtein Distance was:

```text
D_NTL(A,B) = Lev(T(A),T(B)) / max(|T(A)|, |T(B)|)
```

For source case `c`, variability after the first `n` executions was the mean distance over all pairs:

```text
V_c(n) = [1 / choose(n,2)] × Σ_(i<j) D_NTL(G[c,i], G[c,j])
```

The configuration-level estimate gave equal weight to each source case:

```text
V(n) = (1 / C) × Σ_(c=1 to C) V_c(n), where C = 259
```

A cumulative curve was calculated for `n = 2,...,10`. Because ten executions were available, `V(10)` served as the final observed estimate, and the absolute error at an earlier point was:

```text
E(n) = |V(n) - V(10)|
```

The primary tolerance criterion was:

```text
E(n) ≤ ε, where ε = 0.01
```

A candidate point `n` was stable only when every subsequent estimate through execution 10 also remained within tolerance:

```text
|V(m) - V(10)| ≤ ε for every m in {n,...,10}
```

To avoid classifying a late point as convergence without enough posterior observations, a confirmed point `n*` also had to satisfy:

```text
n* ≥ 3 and 10 - n* ≥ 5
```

Under the ten-execution horizon, only executions 3 through 5 could therefore be classified as confirmed convergence points. A later point could indicate observed stabilization but not confirmed convergence.

The tolerance `ε = 0.01` is an operational precision criterion for this study, not a universal threshold. Ninety-five-percent bootstrap confidence intervals for cumulative variability were calculated with 1,000 bootstrap samples by resampling the 259 source cases with replacement; individual pairwise distances were not resampled as independent observations.

When all configurations had a confirmed point, the common conservative repetition count was:

```text
N_conv = max_(m,p) n*[m,p]
```

Executable source: [variability and convergence notebook](../scripts/Analise_Variabilidade_Convergencia_Levenshtein_Tokens_Gherkin.ipynb).

## Metric validation equations

Metric validation used the 259 generated scenarios selected for human assessment. Spearman's rank correlation was the primary association measure. For Manhattan Distance, the coefficient was multiplied by `-1` only for ranking the candidate measures, so that higher oriented values consistently indicated stronger correspondence with higher human scores.

For paired bootstrap comparison of two candidate metrics:

```text
Δρ = ρ*_A - ρ*_B
```

Here, `ρ*_A` and `ρ*_B` are their direction-adjusted Spearman coefficients in the same bootstrap resample. Metric-human confidence intervals used 5,000 paired resamples; pairwise differences between metric correlations used 10,000 paired resamples. Two-sided p-values were adjusted with the Holm procedure.

## Aggregation and statistical comparison equations

For a source case, model, and prompting strategy, mean BERTScore F1 across the ten executions was:

```text
mean_B[c,m,p] = (1 / 10) × Σ_(r=1 to 10) B[c,m,p,r]
```

The case-level coefficient of variation was:

```text
CV[c,m,p] =
  SD(B[c,m,p,1],...,B[c,m,p,10])
  / |mean_B[c,m,p]|
  × 100
```

For the global prompting-strategy comparison, the five model-specific case-level means were averaged within each source case:

```text
mean_B_models[c,p] = (1 / 5) × Σ_(m=1 to 5) mean_B[c,m,p]
```

Prompting strategies were compared with Friedman tests over matched cases, Kendall's W, and two-sided paired Wilcoxon signed-rank tests for zero-shot versus one-shot, zero-shot versus few-shot, and one-shot versus few-shot. Pairwise p-values were adjusted with the Holm procedure; rank-biserial correlation and 95% bootstrap confidence intervals for paired mean differences were reported.

Models were compared under the common selected prompting strategy with the same matched-case procedure. The significance level was `α = 0.05`.

All bootstrap confidence intervals for paired mean differences used 5,000 resamples of the paired source cases. Non-significant differences were not interpreted as evidence of equivalence.
