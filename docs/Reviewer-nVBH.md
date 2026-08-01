
We thank the reviewer for the careful reading and constructive questions. 

The main concern raised in the review is whether the structural patterns observed in the original sweep are supported by direct membership-inference evidence under controlled and repeated evaluation. To address this directly, we added a fully crossed \(3\times2\times2\) MNIST-QNN study over feature-map family, feature-map repetition, and variational depth. The study includes all 12 structural configurations, three independently initialized target models per configuration, repeated attacker training where applicable, and several attack models with different information-access assumptions. In total, the follow-up comprises 36 target models and is used as the main controlled validation of the structural findings. The broader experiments in the submission continue to provide coverage across datasets, QNN/HQNN/QCNN wrappers, and circuit choices.

## Q1. Multi-seed robustness of the structural and attack results

The new factorial evaluates robustness to both target-model initialization and attacker training. Each structural configuration is trained with three independent target seeds. The learned prediction-vector attacker is additionally trained with three attacker seeds, and LiRA uses 16 reference models for each structural configuration. Results are reported as mean \(\pm\) sample standard deviation together with paired hierarchical-bootstrap confidence intervals.

For the loss-based attack, increasing feature-map repetitions from 1 to 5 produces an AUC difference of \(+0.069 \pm 0.029\), with a 95% confidence interval of \([0.049, 0.088]\). Increasing variational depth from 2 to 6 gives \(+0.042 \pm 0.026\), with interval \([0.024, 0.060]\). Relative to EffSU2, the Z and ZZ feature maps increase AUC by \(+0.057 \pm 0.032\), \([0.027, 0.080]\), and \(+0.052 \pm 0.027\), \([0.030, 0.074]\), respectively. The same overall pattern is also visible under LiRA and the label-only attack.

| Attack                      | Factor      | Contrast       | AUC difference\(\pm\) SD | 95% CI              | Paired units |
| --------------------------- | ----------- | -------------- | ------------------------ | ------------------- | ------------ |
| Online LiRA, fixed variance | Repetitions | 5\(-\) 1       | \(+0.044 \pm 0.045\)     | \([0.013, 0.072]\)  | 18           |
| Online LiRA, fixed variance | Depth       | 6\(-\) 2       | \(+0.077 \pm 0.057\)     | \([0.038, 0.118]\)  | 18           |
| Online LiRA, fixed variance | Feature map | Z\(-\) EffSU2  | \(+0.057 \pm 0.030\)     | \([0.038, 0.076]\)  | 12           |
| Online LiRA, fixed variance | Feature map | ZZ\(-\) EffSU2 | \(+0.070 \pm 0.059\)     | \([0.021, 0.117]\)  | 12           |
| Online LiRA, fixed variance | Feature map | ZZ\(-\) Z      | \(+0.014 \pm 0.044\)     | \([-0.026, 0.048]\) | 12           |
| Label-only chord-boundary   | Repetitions | 5\(-\) 1       | \(+0.068 \pm 0.026\)     | \([0.049, 0.085]\)  | 18           |
| Label-only chord-boundary   | Depth       | 6\(-\) 2       | \(+0.047 \pm 0.033\)     | \([0.021, 0.068]\)  | 18           |
| Label-only chord-boundary   | Feature map | Z\(-\) EffSU2  | \(+0.038 \pm 0.035\)     | \([0.004, 0.061]\)  | 12           |
| Label-only chord-boundary   | Feature map | ZZ\(-\) EffSU2 | \(+0.054 \pm 0.039\)     | \([0.022, 0.087]\)  | 12           |
| Label-only chord-boundary   | Feature map | ZZ\(-\) Z      | \(+0.017 \pm 0.026\)     | \([-0.004, 0.035]\) | 12           |

The access-model comparison adds useful nuance. Repetition has a positive pooled effect under loss-threshold, LiRA, and label-only attacks. Z and ZZ are consistently above EffSU2 across these attack families, while the difference between Z and ZZ is not resolved. Under the loss and label-only attacks, the repetition contrast is larger than the depth contrast; under LiRA, depth also has a strong effect. We therefore interpret encoder family and repeated data encoding as privacy-relevant structural factors whose influence persists across target initializations and attack access models, rather than as factors that must dominate every individual attack statistic.

The data partition is held fixed across the factorial so that the paired comparisons isolate structural and initialization effects under the same records. The replicated results therefore quantify robustness to target and attacker initialization within this controlled setting.

## Q2. Evaluation of all configurations and removal of regime-based selection

The follow-up does not reuse the submission's earlier baseline/stress/hard regime labels for statistical inference. Instead, it forms a complete factorial from the three feature-map families and the low/high repetition and depth settings, and applies the same attack suite to every target model. All 36 targets are evaluated using the loss-threshold attack, learned prediction-vector attack, online LiRA, and label-only attack.

| Structural configuration | Target seeds |  Loss-threshold AUC |  Learned-vector AUC |     Online LiRA AUC |      Label-only AUC |
| ------------------------ | -----------: | ------------------: | ------------------: | ------------------: | ------------------: |
| EffSU2, reps=1, depth=2  |            3 | \(0.530 \pm 0.010\) | \(0.558 \pm 0.008\) | \(0.530 \pm 0.032\) | \(0.528 \pm 0.010\) |
| EffSU2, reps=1, depth=6  |            3 | \(0.536 \pm 0.006\) | \(0.510 \pm 0.027\) | \(0.542 \pm 0.020\) | \(0.518 \pm 0.011\) |
| EffSU2, reps=5, depth=2  |            3 | \(0.571 \pm 0.006\) | \(0.517 \pm 0.039\) | \(0.562 \pm 0.029\) | \(0.557 \pm 0.015\) |
| EffSU2, reps=5, depth=6  |            3 | \(0.603 \pm 0.017\) | \(0.568 \pm 0.023\) | \(0.632 \pm 0.037\) | \(0.604 \pm 0.014\) |
| Z, reps=1, depth=2       |            3 | \(0.541 \pm 0.017\) | \(0.481 \pm 0.030\) | \(0.578 \pm 0.014\) | \(0.515 \pm 0.020\) |
| Z, reps=1, depth=6       |            3 | \(0.599 \pm 0.015\) | \(0.542 \pm 0.028\) | \(0.618 \pm 0.023\) | \(0.578 \pm 0.013\) |
| Z, reps=5, depth=2       |            3 | \(0.646 \pm 0.012\) | \(0.602 \pm 0.010\) | \(0.604 \pm 0.025\) | \(0.613 \pm 0.008\) |
| Z, reps=5, depth=6       |            3 | \(0.684 \pm 0.012\) | \(0.638 \pm 0.019\) | \(0.693 \pm 0.023\) | \(0.651 \pm 0.007\) |
| ZZ, reps=1, depth=2      |            3 | \(0.562 \pm 0.012\) | \(0.557 \pm 0.017\) | \(0.583 \pm 0.007\) | \(0.539 \pm 0.015\) |
| ZZ, reps=1, depth=6      |            3 | \(0.604 \pm 0.011\) | \(0.606 \pm 0.034\) | \(0.671 \pm 0.004\) | \(0.612 \pm 0.030\) |
| ZZ, reps=5, depth=2      |            3 | \(0.601 \pm 0.025\) | \(0.595 \pm 0.020\) | \(0.566 \pm 0.023\) | \(0.601 \pm 0.022\) |
| ZZ, reps=5, depth=6      |            3 | \(0.679 \pm 0.018\) | \(0.714 \pm 0.040\) | \(0.727 \pm 0.008\) | \(0.671 \pm 0.025\) |

*Entries are mean \(\pm\) sample SD across three target seeds. For the learned-vector attack, AUC is first averaged across the three attacker seeds for each target.*

The complete table shows that the central trends are not driven by one selected configuration. Higher repetition generally increases attack AUC, particularly for Z and ZZ encoders; increasing depth also raises leakage in most matched comparisons; and the highest attack values are concentrated in the repeated, deeper Z/ZZ configurations. EffSU2 remains comparatively less exposed across the evaluated attack models.

Across all 36 target models, the generalization gap and loss-attack AUC have Pearson correlation \(r=0.948\), with 95% confidence interval \([0.864, 0.982]\), and Spearman correlation \(\rho=0.931\), with interval \([0.710, 0.974]\). This strong within-design association supports the use of the generalization gap as a descriptive privacy-risk proxy in the exploratory sweep. The direct attack results remain the primary evidence: the proxy is not treated as a deterministic predictor, and the new factorial does not rely on the earlier regime labels.

## Q3. Mathematical definition of the factor-attribution figure

We agree that the calculation behind Fig. 8 should be stated explicitly. The figure is constructed as a dataset-wise descriptive ranking of the association between each displayed factor and the prespecified privacy-risk proxy \(y=\Delta_{\mathrm{gen}}\).

Let \(N\) denote the number of runs and \(g_j\) the number of levels of categorical factor \(j\). For a categorical factor, the one-way ANOVA statistic \(F_j\) is transformed into a bounded association score:

\[
s_j^)}
======

\frac{F_j}{F_j + N - g_j}.
\]

For a numeric factor and a displayed numeric product term, respectively,

\[
s_j^)}
======

\left|\operatorname(X_j,y)\right|,
\qquad
s_
==

\left|\operatorname{corr}(X_jX_k,y)\right|.
\]

The displayed percentage is the normalized share of the aggregate association score:

\[
A_j
===

100\frac{s_j}{\sum_k s_k},
\qquad
\sum_j A_j=100.
\]

The component scores are nonnegative, dimensionless, and bounded by one. A value such as 22.5% therefore means that the corresponding term accounts for 22.5% of the total displayed factor-association score within that dataset. It is not presented as a Sobol decomposition, a causal effect, or a conditional coefficient from a joint model.

In the revision, we will replace the term *contribution* with *normalized factor-association share* and add the equations directly to the manuscript. We will also make clear that Fig. 8 is an exploratory broad-sweep summary. The controlled evidence for the feature-map, repetition, and depth findings comes from the paired direct-MIA contrasts in the fully crossed factorial, which do not depend on the normalization used in Fig. 8.

## Q4. Architecture controls

We added architecture-level comparisons using QNN, HQNN, QCNN, and a classical MLP with a comparable small parameter budget to the QNN-based models. The comparison is repeated over three structural roles and three target initializations, and the revision reports the exact trainable-parameter counts together with the main-stack quantum gate counts.

Relative to QNN, the classical MLP improves test accuracy by \(+0.146 \pm 0.093\), with 95% confidence interval \([0.062, 0.259]\). Its generalization-gap and loss-AUC differences are not resolved. QCNN improves test accuracy by \(+0.190 \pm 0.052\), \([0.148, 0.249]\), reduces the generalization gap by \(-0.041 \pm 0.042\), \([-0.083, -0.007]\), and reduces loss-attack AUC by \(-0.019 \pm 0.025\), \([-0.040, -0.001]\). The HQNN intervals overlap zero.

These comparisons show that the magnitude of the observed structural privacy signal depends on the surrounding model architecture. In particular, QCNN achieves higher predictive performance while exhibiting a smaller gap and lower loss-based membership leakage than the corresponding QNN role. The architecture experiments are reported as complete-wrapper comparisons, with parameter and gate counts made explicit, rather than as perfectly matched causal ablations. This distinction is now stated directly in the manuscript.

## Q5. Expanded attack evaluation and bounded conclusions

We broadened the direct attack evaluation to cover substantially different information-access assumptions. The added attacks include a scalar loss threshold, a learned prediction-vector attacker, calibrated online and offline LiRA variants, and a class-label-only boundary attack. Together, these experiments test whether the structural signal is specific to a single attack statistic or remains visible under stronger calibration and more restricted output access.

| Attack                           | Information access                                   |                 AUC |          TPR@5% FPR |         TPR@10% FPR |
| -------------------------------- | ---------------------------------------------------- | ------------------: | ------------------: | ------------------: |
| Loss threshold                   | True-label probability and known candidate label     | \(0.596 \pm 0.052\) | \(0.061 \pm 0.024\) | \(0.135 \pm 0.039\) |
| Learned prediction-vector        | Full prediction vector and derived statistics        | \(0.574 \pm 0.065\) | \(0.099 \pm 0.044\) | \(0.157 \pm 0.061\) |
| Online LiRA, fixed variance      | True-label probability and calibrated reference QNNs | \(0.609 \pm 0.063\) | \(0.128 \pm 0.056\) | \(0.192 \pm 0.069\) |
| Online LiRA, per-record variance | True-label probability and calibrated reference QNNs | \(0.594 \pm 0.057\) | \(0.086 \pm 0.034\) | \(0.155 \pm 0.063\) |
| Offline LiRA, fixed variance     | True-label probability and calibrated reference QNNs | \(0.517 \pm 0.029\) | \(0.066 \pm 0.027\) | \(0.128 \pm 0.035\) |
| Label-only chord-boundary        | Predicted labels only and held-out anchors           | \(0.582 \pm 0.052\) | \(0.077 \pm 0.027\) | \(0.139 \pm 0.030\) |

The strongest average result is obtained by online LiRA with fixed variance, while the loss-threshold and label-only attacks preserve the same principal structural ordering. The per-record and offline LiRA variants are weaker in this setting, which is also reported in full. The agreement across attacks with different access assumptions supports the conclusion that the observed leakage pattern is not an artifact of one particular attack implementation.

The revision also narrows the scope of the claims to match the evidence. The confirmatory analysis concerns feature-map family, repetition, and depth in the controlled MNIST-QNN factorial. Width, entangler, gate, and padding results from the broader sweep remain exploratory. The evaluated datasets consist of synthetic tasks and compressed four-class MNIST under noiseless simulation. References to sensitive applications are used to motivate why membership privacy matters in QML; they are not presented as evidence of deployment readiness.

Classical CNN/kernel models, quantum-kernel methods, regularized or early-stopped QNNs, differentially private training, and calibration-based defenses are valuable directions for a broader follow-up study, but they are not required for the narrower claim established here: within the evaluated QML setting, encoder family and repeated data encoding produce reproducible differences in generalization behavior and direct membership-inference vulnerability, and these differences remain visible across multiple target initializations, attacker initializations, and attack access models.
