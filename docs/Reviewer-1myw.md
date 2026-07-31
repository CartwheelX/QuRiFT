# Response to Reviewer 1myw: Direct Geometry and Robustness Under Backend-Derived Noise

Thank you for the detailed follow-up and for identifying two important gaps in the original submission. We agree that the geometric interpretation introduced in Appendix D was not directly measured, and that results obtained only under noiseless simulation cannot establish whether the observed structural effects remain visible under finite-shot uncertainty and backend noise.

Following the review, we began additional multi-seed geometry and noisy finite-shot experiments. These analyses required independently trained target checkpoints, followed by repeated quantum-circuit simulations across several shot counts, simulator seeds, and noise conditions. They therefore required substantially more simulation time and computational resources than a conventional ablation. The analyses are now complete.

## Backend-Derived Noise and Finite Shots

For quick sanity check, we evaluated five representative structural configurations that vary feature-map family, encoder repetition, and variational depth. The configurations include EfficientSU2, Z, and ZZ feature maps, repetition counts of 1 and 5, and variational depths of 2 and 6. Their exact-inference loss-MIA AUCs ranged from 0.521 to 0.700. This subset was fixed before examining any finite-shot or backend-noise outcomes.

Each structural configuration was independently trained with three target-model seeds, producing 15 saved target checkpoints. For every checkpoint, we evaluated the same member and non-member records under three execution settings:

1. Exact noiseless inference
2. Ideal finite-shot Aer simulation
3. Aer simulation using a noise model derived from the `ibm_kingston` backend calibration, including gate, readout, and thermal-relaxation errors

The finite-shot evaluations used 128, 512, and 1,024 shots, each with ten seeds. The experiment therefore contains 15 independently trained target models, 900 finite-shot executions, and 15 exact evaluations.

Finite-shot uncertainty and backend-derived noise reduced the magnitude of membership leakage, but did not remove the association between structural configuration and mean attack AUC in the evaluated subset. Configurations with weaker leakage under exact inference continued to show weaker mean leakage under noisy evaluation, while configurations with stronger leakage continued to show stronger mean leakage.

The loss-MIA AUC difference between ZZ with repetitions=5 and depth=6, and EfficientSU2 with repetitions=1 and depth=2, decreased from 0.179 ± 0.030 under exact inference to 0.096 ± 0.012 at 128 noisy shots. The difference was 0.124 ± 0.026 at 512 noisy shots and 0.132 ± 0.036 at 1,024 noisy shots. The mean Spearman correlation between the noisy and exact-inference AUC results was 0.820 ± 0.155 at 128 noisy shots, 0.920 ± 0.092 at 512 shots, and 0.960 ± 0.052 at 1,024 shots.

These results show that noise does not affect every configuration uniformly. It attenuates the observable membership signal and introduces greater variability at lower shot counts. Nevertheless, the relationship between structural configuration and membership leakage remains detectable across independently trained target models. This provides a targeted robustness check for our main claim that encoder design and repetition condition downstream membership risk.

### Membership Leakage Under Finite Shots and Backend-Derived Noise

| Condition | EfficientSU2, reps=1, depth=2 AUC | ZZ, reps=5, depth=6 AUC | AUC difference: ZZ minus EfficientSU2 | Spearman ρ ± SD |
|---|---:|---:|---:|---:|
| Exact | 0.521 ± 0.018 | 0.700 ± 0.037 | +0.179 ± 0.030 | 1.000 |
| Ideal, 128 shots | 0.518 ± 0.015 | 0.671 ± 0.033 | +0.153 ± 0.018 | 0.900 ± 0.115 |
| Ideal, 512 shots | 0.520 ± 0.012 | 0.691 ± 0.031 | +0.171 ± 0.021 | 0.990 ± 0.032 |
| Ideal, 1,024 shots | 0.521 ± 0.015 | 0.696 ± 0.037 | +0.175 ± 0.027 | 0.990 ± 0.032 |
| Backend-noisy, 128 shots | 0.526 ± 0.020 | 0.622 ± 0.031 | +0.096 ± 0.012 | 0.820 ± 0.155 |
| Backend-noisy, 512 shots | 0.527 ± 0.013 | 0.651 ± 0.034 | +0.124 ± 0.026 | 0.920 ± 0.092 |
| Backend-noisy, 1,024 shots | 0.525 ± 0.019 | 0.657 ± 0.034 | +0.132 ± 0.036 | 0.960 ± 0.052 |

Individual simulator runs sometimes reordered configurations with similar AUCs, especially at 128 shots. Mean Spearman correlation with the exact five-configuration ranking was 0.820±0.155 at 128 noisy shots, 0.920±0.092 at 512 shots, and 0.960±0.052 at 1,024 shots. Noise therefore partially mitigates leakage and introduces local variability, but the structural dependence remains detectable. This provides targeted support for our main claim that encoder design and repetition condition downstream membership risk. We will report the calibration timestamp, confirm that no ideal fallback was accepted.

AUC differences are mean±sample SD across three independently trained target seeds after averaging repetitions within each checkpoint. Spearman values are mean±sample SD across ten simulator seeds.

## Direct Post-Encoder Geometry

We also directly evaluated the geometric quantities introduced in Appendix D. We computed the pure-state Hilbert-Schmidt, or fidelity, kernel immediately after the fixed encoder and before the trainable variational circuit or classifier.

For paired repetition 5 minus repetition 1 contrasts, increasing encoder repetition produced the following changes:

- The within-class minus between-class similarity gap changed by −0.124 ± 0.061, with a 95% hierarchical-bootstrap confidence interval of [−0.158, −0.079].
- Centered kernel-label alignment changed by −0.208 ± 0.132, with a confidence interval of [−0.285, −0.109].
- Kernel effective rank increased by 49.747 ± 37.448, with a confidence interval of [15.791, 83.870].

The reduction in class-similarity gap and kernel-label alignment, together with the increase in effective rank, indicates that repeated encoding produces a higher-rank but less class-aligned representation. These measurements directly support the first part of the proposed pathway: changing the fixed encoder changes the geometry presented to the trainable portion of the model.

The geometry uncertainty analysis uses 5,000 hierarchical percentile-bootstrap replicates over 12 unique paired repetition contrasts. For the fixed MNIST subset, nominal data seeds can produce identical encoded states. These duplicates are not counted as independent geometric effects.

## Revised Interpretation of the Proposed Pathway

We agree with the reviewer that the original wording could be interpreted as making a stronger causal claim than the experiments support. Our hypothesis is not that encoder geometry bypasses overfitting or directly reveals whether a record belongs to the training set.

The evidence supports the following empirical pathway:

> **Encoder design → post-encoder geometry → downstream generalization asymmetry → membership signal**

The direct fidelity-kernel analysis supports the association between encoder design and post-encoder geometry. The multi-seed factorial analysis supports associations between encoder repetition, generalization gap, and loss-MIA AUC. The generalization gap is also strongly associated with loss-MIA AUC:

> **Spearman ρ = 0.931, 95% CI [0.710, 0.974]**

The noisy finite-shot results provide a further robustness check. Backend noise reduces the measurable attack advantage, but does not remove the association between the evaluated structural configurations and mean membership leakage. This suggests that the reported structural effect is not solely an artifact of exact noiseless inference.

These observations do not causally identify the geometry-to-generalization link independently of encoder choice. We do not intervene on geometry while holding the encoder fixed, and we therefore cannot claim that the measured geometric quantities causally determine the generalization gap.

We will replace phrases such as “geometric leakage mechanism” and “geometry creates train-test separation” with more precise descriptions, including “empirically supported geometric pathway” and “encoder-induced, overfitting-mediated membership risk.” We will also state explicitly that causal mediation has not been identified.

## Attack Breadth

We have added separate threshold attacks based on:

- Per-record loss
- Prediction confidence
- Prediction entropy
- Classification margin
- Maximum predicted probability
- Prediction correctness

These attacks complement the original learned prediction-vector attacker and help identify which output statistics expose the membership signal.

For binary classification tasks, confidence, entropy, margin, and maximum probability often induce equivalent or nearly equivalent score rankings. We therefore treat them as related confidence-based attacks rather than presenting them as fully independent confirmations.

In the revised evaluation, we will provide additional experiments covering reference-model attacks such as LiRA, label-only attacks, and threshold-based attacks as baselines. These experiments are being done, and the results will be posted shortly in the comments on OpenReview.



## Presentation Changes

We will add an overview figure showing the complete evaluation pathway:

> **Classical input → fixed quantum encoder → post-encoder kernel geometry → trainable variational circuit → target-model outputs → generalization asymmetry → membership inference**

The figure will distinguish quantities measured before target-model training from quantities measured after optimization. In particular, the fidelity-kernel geometry and train-test MMD are measured immediately after the fixed encoder, whereas the generalization gap and membership-inference results are measured after training.

We thank the reviewer for prompting these analyses. They materially improved both the empirical validation and the precision of the paper’s central claim.
