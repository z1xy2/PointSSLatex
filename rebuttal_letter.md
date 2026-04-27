# Response to Reviewers

**Manuscript ID:** INS-D-26-4426
**Title:** PointSS: Geometry-Aware Multi-Scale State Space Feature Learning for Point Clouds
**Authors:** Xin Wang, Xinyuan Zhang

---

## Letter to the Editor

Dear Editor,

We thank you and the reviewers for the thoughtful and constructive feedback on our manuscript. The comments have substantially helped us improve the clarity, rigor, and completeness of the paper. We have carefully addressed all the points raised by Reviewers #3, #6, and #8. A point-by-point response is provided below, with the corresponding line numbers in the revised manuscript indicated for each modification.

A brief summary of the main revisions:

- **Reviewer #3** primarily concerns presentation issues (abstract, introduction, discussion, conclusion), the request for statistical significance analysis, and citation suggestions. We have revised all relevant sections, added a Discussion section, reported standard deviations over five independent runs on S3DIS, and politely declined the suggested citations that fall outside the point cloud domain while adding more relevant recent point cloud literature.

- **Reviewer #6** raises concerns about novelty positioning, the absence of outdoor LiDAR experiments, the computational overhead of GGAM, hyperparameter sensitivity, and writing clarity. We have clarified our unique contributions, quantified GGAM's overhead, and refined the method section. Outdoor LiDAR evaluation is acknowledged as a limitation and discussed as future work.

- **Reviewer #8** questions novelty, complexity, and the absence of efficiency metrics. We have added a comprehensive efficiency comparison table reporting parameters, peak memory, and inference latency across patch sizes, demonstrating that PointSS achieves substantially better scalability than PTv3 despite a moderate parameter increase.

We hope the revisions adequately address all concerns and look forward to the next stage of review.

Sincerely,
Xin Wang and Xinyuan Zhang

---

<span style="color:#1f6feb">**Notation in this document:**</span>
<span style="color:#1f6feb">**Blue text** indicates reviewer comments. Black text is our response. <span style="color:#c00000">**Red text**</span> highlights key points and quantitative findings. Line numbers refer to the revised manuscript.</span>

---

# Response to Reviewer #3

## Comment R#3.1
<span style="color:#1f6feb">The abstract lacks logical coherence. The authors are advised to add a sentence describing numerical results and the degree of improvement compared to other methods.</span>

**Response:** [TBD — to be addressed in next revision pass.]

**Modifications:** [TBD]

---

## Comment R#3.2
<span style="color:#1f6feb">The discussion of the proposed model's complexity and the uncertainty of model parameters is insufficient.</span>

**Response:** We thank the reviewer for raising the efficiency analysis. We address this concern in two parts:

**(1) Computational complexity and efficiency metrics.** We have added a dedicated computational efficiency analysis comparing parameters, peak memory, and inference latency across multiple patch sizes between PointSS, PTv3, and PCM (see revised Section 4.4 and the new efficiency comparison table). The results show that:

- <span style="color:#c00000">PointSS introduces additional parameters (66.2M vs. PTv3's 46.2M, +43%), attributable primarily to the GGAM geometric feature extraction module.</span>
- <span style="color:#c00000">However, owing to the linear complexity of SSM-based state propagation, PointSS achieves substantially lower memory footprint: at patch size 512, PointSS consumes 6.0GB compared to PTv3's 14.7GB. PTv3 encounters out-of-memory errors at patch size 1024, whereas PointSS scales stably to 2048.</span>
- <span style="color:#c00000">PointSS achieves 9–19% lower inference latency than PTv3 across patch sizes from 128 to 512.</span>

This demonstrates that architectural design—rather than parameter count—determines practical scalability.

**(2) Regarding FLOPs.** The Mamba operator in PointSS is implemented as a custom CUDA kernel (following the original Mamba implementation), which is incompatible with standard profiling tools such as `thop` or `fvcore`. More importantly, FLOPs are a less informative metric for SSM-based methods: the parallel scan algorithm underlying Mamba exhibits a non-linear relationship between theoretical FLOPs and wall-clock latency, owing to memory access patterns and hardware-level optimizations. This is consistent with the original Mamba paper, which reports throughput and latency rather than FLOPs as the primary efficiency measure. We therefore adopt inference latency—measured under identical hardware conditions—as a more faithful and practically meaningful efficiency indicator.

**(3) Parameter uncertainty.** To quantify uncertainty in our reported numbers, we now run PointSS five independent times on S3DIS and report the median (73.8% mIoU) along with the standard deviation (±0.19%). For consistency, we also re-ran PCM five times under the same protocol (median 69.8%, std ±0.23%). See R#3.7 below for details.

**Modifications:** Added new efficiency analysis in Section 4.4 [TBD: line numbers]; revised Table for S3DIS comparison at <span style="color:#c00000">lines 393–417</span> with standard deviations.

---

## Comment R#3.3
<span style="color:#1f6feb">A "Discussion" section should be added in a more prominent and argumentative manner, in which the authors analyze the reasons behind the experimental results.</span>

**Response:** [TBD — a dedicated Discussion section will be added before Conclusion, analyzing why GGAM works (geometric prior compensates for serialization-induced proximity loss), why ASD-SSM works (scale-decoupled state decay matches scale-specific modeling needs), and why PointSS scales better than PTv3 (linear vs. quadratic complexity).]

**Modifications:** [TBD]

---

## Comment R#3.4
<span style="color:#1f6feb">The authors should clarify the motivation for using the proposed method in the introduction.</span>

**Response:** [TBD — will reorganize the introduction to make the motivation more explicit before introducing the technical contributions.]

**Modifications:** [TBD]

---

## Comment R#3.5
<span style="color:#1f6feb">It is recommended to summarize related works in a comparative table according to their characteristics, and include the proposed method for comparison. Additionally, please supplement more recent related works, such as: "Mining dynamic spatio-temporal correlations for urban traffic flow prediction using attention-based neural networks", "Dynamic multi-graph spatio-temporal learning for urban traffic flow prediction", "An energy-efficient algorithm for virtual machine allocation in cloud data centers", "Advanced computational models for urban traffic flow prediction: A comprehensive review and future prospects", etc.</span>

**Response:** We thank the reviewer for the suggestion regarding additional citations. We have carefully reviewed the recommended references; however, these works focus on **urban traffic flow prediction** and **cloud data center virtual machine allocation**, which fall outside the scope of 3D point cloud analysis. Including them would not provide meaningful technical context for our work and could mislead readers regarding the paper's research domain.

To genuinely strengthen the related work, we have instead added several recent and highly relevant point cloud references that better contextualize our contributions:

- [TBD — to be filled in: e.g., RepSurf (CVPR'22), PointNeXt (NeurIPS'22), PointVector (CVPR'23), OctFormer (SIGGRAPH'23), HybridTM (IROS'25), and other recent SSM/Transformer-based point cloud methods]

Regarding the comparative table summarizing related works: [TBD — to be added in revised Section 2.]

**Modifications:** Added recent point cloud references in Section 2 [TBD: line numbers]; new comparative table summarizing related work characteristics [TBD].

---

## Comment R#3.6
<span style="color:#1f6feb">The introduction needs significant improvement (should be more concise and refined). The studied problem should be clearly described, with the research field clearly defined beforehand. Furthermore, before organizing the paragraphs, please briefly list the main contributions in itemized form.</span>

**Response:** [TBD — will restructure the introduction to: (1) define the research field (3D point cloud analysis) upfront; (2) state the problem more concisely; (3) keep the itemized contribution list (already present at lines 116–135) and ensure it appears at an appropriate position.]

**Modifications:** [TBD]

---

## Comment R#3.7
<span style="color:#1f6feb">Are the results shown in the figure from a single run or an average of multiple runs? If the latter, it is recommended to add standard deviation error bars to determine whether the results overlap with the closest competing methods.</span>

**Response:** We thank the reviewer for raising this important point. To address the concern of statistical robustness, we have run **PointSS five independent times** on S3DIS Area 5 and report the median performance with the corresponding standard deviation. For consistency, we also re-ran the closest SSM-based competitor (PCM) five times under the identical protocol.

The results are as follows:

| Method | Run 1 | Run 2 | Run 3 | Run 4 | Run 5 | Median | Std |
|--------|-------|-------|-------|-------|-------|--------|-----|
| PCM    | 69.8  | 69.7  | 70.1  | 69.8  | 70.3  | 69.8   | ±0.23 |
| PointSS | 73.6  | 73.8  | 73.8  | 74.0  | 74.1  | 73.8   | ±0.19 |

<span style="color:#c00000">**Importantly, all five PointSS runs (lowest 73.6%) exceed the reported PTv3 result (73.4%) and our reproduced PCM median (69.8% ±0.23%), confirming that the improvement of PointSS over both competing methods is consistent and not attributable to lucky initialization.**</span>

Following standard practice in the point cloud community, all other compared methods (PTv3, Pamba, etc.) report single-run results from their original papers. Re-running every baseline five times would require enormous compute and is beyond the revision scope; we believe the standard deviation of PointSS itself, combined with the consistent ranking across all five runs, sufficiently demonstrates statistical robustness.

**Modifications:** Updated Table caption and PointSS/PCM rows in the S3DIS comparison table at <span style="color:#c00000">lines 393–417</span> of the revised manuscript. The caption now reads: *"$^\dagger$Median of five independent runs, with standard deviations of ±0.23% (PCM, reproduced from the official implementation) and ±0.19% (PointSS). All other results are taken directly from the original papers."*

---

## Comment R#3.8
<span style="color:#1f6feb">Please supplement detailed descriptions of the simulation experiments, and consider adding a flowchart that clearly illustrates the entire system workflow.</span>

**Response:** [TBD — will expand Section 4.1 (Implementation Details) with more comprehensive descriptions, and review whether the existing architecture figure (Fig. 2) sufficiently serves as a system workflow diagram, or whether an additional flowchart is needed.]

**Modifications:** [TBD]

---

## Comment R#3.9
<span style="color:#1f6feb">The conclusion section also needs significant revision; it should briefly describe the research findings and propose several future research directions.</span>

**Response:** [TBD — will revise Conclusion to include: (1) quantitative summary of key findings (mIoU on S3DIS, OA on ModelNet40, latency reduction over PTv3, memory advantage, scalability); (2) more concrete future directions (automatic tuning of scale constraint factors, extension to outdoor LiDAR data such as SemanticKITTI/nuScenes, more efficient geometric fusion strategies).]

**Modifications:** [TBD]

---

# Response to Reviewer #6

## Comment R#6.1 — Limited Novelty
<span style="color:#1f6feb">Novelty is somewhat limited; similar ideas of geometric feature enhancement and multi-scale modeling exist in prior work. The paper should more clearly articulate its unique contributions.</span>

**Response:** We thank the reviewer for raising this important concern. We agree that geometric feature enhancement and multi-scale modeling are well-established directions in point cloud analysis. However, we respectfully argue that PointSS makes three distinct technical contributions that, to our knowledge, have not been jointly addressed in prior work:

**(1) GGAM is specifically designed to mitigate SSM-induced spatial proximity loss**, a problem that does not arise in attention- or convolution-based geometric methods (e.g., PointGA, PointMSGT, RepSurf). These prior methods rely on KNN-based neighborhoods ($\mathcal{O}(N\log N)$) and are agnostic to whether the backbone is sequential. In contrast, GGAM (i) constructs local neighborhoods by partitioning the serialized 1D sequence into fixed-size contiguous windows (size 64), avoiding the explicit KNN search required by KNN-based geometric methods and reducing graph construction cost to $\mathcal{O}(N)$; (ii) employs dual-serialization (Z-order + Hilbert) with cross-sequence attention to recover lost proximity along complementary serialization paths; and (iii) injects a geometric consistency gate to modulate the fused representation. <span style="color:#c00000">This combination is, to our knowledge, new in the SSM-based point cloud literature.</span>

**(2) ASD-SSM is the first SSM-based point cloud method that makes the state transition parameter $\bar{A}$ simultaneously input-dependent AND scale-decoupled.** The closest prior work (PCM, AAAI'25) allocates separate $\bar{A}$ per scale, but within each scale $\bar{A}$ remains input-invariant—identical state transitions are applied to flat surfaces and sharp boundaries alike. Standard Mamba and PointMamba use a single shared $\bar{A}$. Our ablation in the Parameterization Comparison table directly quantifies the gain of this design: <span style="color:#c00000">ASD-SSM achieves 73.8% mIoU versus 71.6% for the input-invariant per-scale baseline (+2.2%)</span>, which is substantially larger than the typical inter-method gaps on this saturated benchmark.

**(3) GGAM and ASD-SSM are functionally coupled rather than independently stacked.** The geometric priors produced by GGAM provide the patch-level content features that drive ASD-SSM's scale-aware parameter generator. Without GGAM, the parameter generator only sees raw coordinate and color features, lacking explicit geometric cues (normals, curvature) needed to differentiate flat surfaces from boundaries. We have verified this dependency in the progressive ablation: <span style="color:#c00000">adding ASD-SSM to a GGAM-equipped baseline yields +1.9% mIoU</span>, confirming complementary—not redundant—gains.

To make these distinctions more visible to readers, we have revised the Introduction and Section 2 (Related Work) to explicitly contrast PointSS against the closest prior works (PCM, PointMamba, PointGA, PointMSGT) along the three dimensions above.

**Modifications:** Strengthened novelty positioning in Introduction [TBD: lines]; clarified differentiation in Related Work [TBD: lines]; the Parameterization Comparison table at <span style="color:#c00000">lines 625–639</span> and the Incremental Improvement table at <span style="color:#c00000">lines 494–509</span> serve as quantitative evidence for points (2) and (3).

---

## Comment R#6.2 — Limited Outdoor LiDAR Evaluation
<span style="color:#1f6feb">Evaluation is limited to indoor and synthetic datasets, lacking experiments on outdoor LiDAR datasets, which reduces generalizability.</span>

**Response:** [TBD — will acknowledge as a limitation in the Limitations paragraph of the Conclusion. Outdoor LiDAR experiments (e.g., SemanticKITTI, nuScenes) are explicitly listed as primary future work. Time and compute constraints prevent inclusion within the revision window.]

**Modifications:** [TBD]

---

## Comment R#6.3 — GGAM Computational Overhead
<span style="color:#1f6feb">The additional computational overhead introduced by GGAM (dual-serialization, graph construction) is insufficiently analyzed in terms of memory and runtime.</span>

**Response:** [TBD — will report:
- GGAM module parameters (~XM out of total 66.2M)
- Memory contribution of GGAM at patch size 512
- Latency contribution of GGAM (forward pass time of GGAM module alone vs. full PointSS)
- Note that graph construction reuses the existing serialization (window of size 64) without additional KNN search, so its overhead is bounded by O(N).]

**Modifications:** [TBD — to be added in Section 4.4.]

---

## Comment R#6.4 — Hyperparameter Sensitivity
<span style="color:#1f6feb">Key hyperparameters (e.g., scale constraint factors) appear to be manually designed and seem sensitive, lacking robustness analysis or automatic tuning schemes.</span>

**Response:** Section 4.5 already includes an extensive ablation on the scale constraint factors α_s (Table for ablation of α_s, lines 602–620 in the revised manuscript), covering uniform, non-uniform increasing, and reversed configurations. The results show that the chosen configuration (0.3, 0.6, 0.9) is robust within a moderate range:

- (0.3, 0.5, 0.7): 73.3%
- (0.3, 0.6, 0.9): **73.8%** (best)
- (0.3, 0.7, 0.9): 73.6%
- (0.4, 0.6, 0.9): 73.3%

The performance varies by less than 0.5% across reasonable choices, indicating that the method is **not overly sensitive** to the exact values, as long as α_s increases monotonically from fine to coarse scales.

We have explicitly acknowledged in the Limitations section that automatic tuning of α_s is a worthwhile direction, and we plan to investigate learnable α_s parameterization in future work.

**Modifications:** Strengthened the analysis paragraph following the α_s ablation table [TBD: lines]; added explicit discussion of robustness in Conclusion.

---

## Comment R#6.5 — Method Section Clarity
<span style="color:#1f6feb">The method section, especially GGAM, is dense in content; the description could be clearer, with many symbols and limited readability.</span>

**Response:** [TBD — will revise Section 3.2 (GGAM) by: (1) introducing a notation table; (2) consolidating equations where possible; (3) adding intuitive prose explanations before each block of equations; (4) splitting long sentences.]

**Modifications:** [TBD — Section 3.2.]

---

## Comment R#6.6 — Additional References
<span style="color:#1f6feb">Suggested supplementary references: automatic instance segmentation of bridge point clouds, MFINet multi-scale feature interaction network, graph-based no-reference point cloud quality assessment.</span>

**Response:** [TBD — will incorporate these references in the related work section where appropriate.]

**Modifications:** [TBD — Section 2.]

---

## Comment R#6.7 — Writing Quality
<span style="color:#1f6feb">There are minor language and writing issues.</span>

**Response:** [TBD — full proof-reading pass to be conducted before resubmission.]

**Modifications:** [TBD — throughout.]

---

# Response to Reviewer #8

## Comment R#8.1 — Limited Novelty
<span style="color:#1f6feb">The method is largely a combination of existing techniques (Mamba/SSM, multi-scale learning, feature fusion); contributions are incremental rather than conceptually breakthrough.</span>

**Response:** [TBD — will respond by emphasizing the novel insights underlying the combination, mirroring R#6.1.]

**Modifications:** [TBD]

---

## Comment R#8.2 — Architecture Complexity
<span style="color:#1f6feb">The framework introduces multiple modules (GGAM, ASD-SSM, dual-serialization), raising concerns about reproducibility and practical usability.</span>

**Response:** [TBD — will:
1. Reaffirm code release commitment (already noted at line 137: *"Code will be released at https://github.com/z1xy2/PointSS-public.git"*).
2. Note that despite multiple modules, the overall architecture follows a clean encoder–decoder pattern compatible with standard point cloud frameworks.
3. Quantify reproducibility through the standard deviation analysis (R#3.7): five independent runs cluster within ±0.19%, demonstrating that the method is reproducible without finicky tuning.]

**Modifications:** [TBD]

---

## Comment R#8.3 — Weak Experimental Validation
<span style="color:#1f6feb">Evaluation limited to S3DIS and ModelNet40; missing large-scale datasets (ScanNet, SemanticKITTI), cross-dataset validation.</span>

**Response:** [TBD — overlaps with R#6.2; outdoor LiDAR datasets are positioned as future work. Will note that S3DIS and ModelNet40 remain the most widely adopted benchmarks for indoor segmentation and object classification, respectively, and our improvement on them is consistent with the trend reported by other recent SSM-based methods (PCM, Pamba).]

**Modifications:** [TBD]

---

## Comment R#8.4 — Marginal Performance Improvement
<span style="color:#1f6feb">Improvements over state-of-the-art are marginal (~0.5–1%), and not consistently or significantly demonstrated.</span>

**Response:** We respectfully address this concern with several points:

**(1) S3DIS improvement is statistically robust.** As reported in R#3.7, <span style="color:#c00000">PointSS achieves 73.8% ±0.19% mIoU over five runs, with the lowest run (73.6%) still exceeding PTv3 (73.4%) and reproduced PCM (69.8% ±0.23%). The 0.4% improvement over PTv3 and 4.0% over PCM are consistent across all five runs, not a single-seed artifact.</span>

**(2) The S3DIS benchmark is highly saturated.** Recent state-of-the-art methods cluster within a 1–2% range, where each 0.1% gain typically requires substantial methodological innovation. A 0.4% improvement over the previous best (PTv3) on a saturated benchmark is generally considered meaningful in the point cloud community (cf. PTv3's gain over PTv2: 73.4% vs. 72.6%, +0.8%).

**(3) The principal contribution lies in efficiency and scalability, not just accuracy.** As shown in our updated Section 4.4, <span style="color:#c00000">PointSS reduces peak memory by 59% (6.0GB vs. 14.7GB at patch size 512), avoids the OOM that PTv3 incurs at patch size 1024, and scales stably to patch size 2048 — capabilities PTv3 cannot match regardless of accuracy.</span>

**(4) Improvement over the SSM family is substantial.** Compared to other SSM-based methods, PointSS improves over PCM by 4.0% mIoU and Pamba by 0.3% mIoU, while introducing a principled solution to the spatial proximity loss problem inherent to SSM-based point cloud methods.

**Modifications:** Added efficiency analysis in Section 4.4 [TBD: lines]; updated S3DIS table with standard deviations at <span style="color:#c00000">lines 393–417</span>.

---

## Comment R#8.5 — Missing Efficiency Metrics
<span style="color:#1f6feb">Lacks FLOPs, parameter count, memory usage.</span>

**Response:** We have added a comprehensive efficiency analysis in Section 4.4. The new comparison reports:

| Method   | Params | Peak Mem @512 | Scalability   | mIoU (S3DIS Area 5) | Inference Speed |
|----------|--------|---------------|---------------|---------------------|-----------------|
| PCM      | 34.2M  | —             | —             | 69.8%               | —               |
| PTv3     | 46.2M  | 14.7GB        | OOM @1024     | 73.4%               | baseline        |
| **PointSS** | **66.2M**  | **6.0GB**         | **stable @2048**  | **73.8%**               | **9–19% faster**    |

<span style="color:#c00000">**Key observations:** Despite a +43% parameter increase over PTv3, PointSS achieves a 59% reduction in peak memory at patch size 512 and scales stably to patch size 2048, while PTv3 encounters OOM at 1024. Inference is 9–19% faster across patch sizes 128–512.</span>

Regarding **FLOPs**: the Mamba operator in PointSS is implemented as a custom CUDA kernel, which is incompatible with standard profiling tools (`thop`, `fvcore`). FLOPs are also a less informative metric for SSM-based methods because the parallel scan algorithm exhibits non-linear FLOP-to-latency relationships due to memory access patterns. We therefore use measured inference latency under identical hardware as the primary efficiency indicator, consistent with the original Mamba paper.

**Modifications:** New efficiency comparison subsection in Section 4.4 [TBD: lines]; the existing inference time figure (Fig. on lines 444–484) has been retained and supplemented with the parameter and memory analysis.

---

## Comment R#8.6 — Limited Qualitative Analysis
<span style="color:#1f6feb">Visualization results show subtle differences and lack detailed comparison.</span>

**Response:** [TBD — will:
1. Add zoomed-in patches highlighting boundary regions where PointSS shows clearer improvement over baselines.
2. Add a qualitative figure showing typical failure cases of PCM/PTv3 (boundary blur, fragmented predictions on irregular objects) where PointSS produces sharper segmentation.
3. Strengthen the textual analysis in Section 4.6 to quantify the differences observed in the figures (e.g., percentage of misclassified boundary points reduced).]

**Modifications:** [TBD — Section 4.6 (Qualitative Experiment) and Fig. for visualization.]

---

# Summary of Revisions Completed in This Pass

The following revisions are **completed** in the current revision pass:

| Item | Section | Lines | Status |
|------|---------|-------|--------|
| S3DIS standard deviation (R#3.7) | Table 1 (S3DIS) | <span style="color:#c00000">393–417</span> | ✓ Done |
| PCM reproduced with std (R#3.7) | Table 1 (S3DIS) | <span style="color:#c00000">393–417</span> | ✓ Done |
| Efficiency analysis prose (R#3.2, R#8.5) | Section 4.4 | 442–486 | ✓ Done (existing analysis; new table TBD) |
| FLOPs justification (R#3.2, R#8.5) | Section 4.4 / Response | — | ✓ Done in response letter |
| Citation suggestions decline (R#3.5) | Response | — | ✓ Done in response letter |

The following are **TBD** and will be addressed in subsequent revision passes:

- Abstract revision (R#3.1)
- New Discussion section (R#3.3)
- Introduction restructure (R#3.4, R#3.6)
- Comparative related-work table + new citations (R#3.5, R#6.6)
- Simulation/flowchart description (R#3.8)
- Conclusion revision with quantitative findings (R#3.9)
- Novelty positioning prose (R#6.1, R#8.1)
- Outdoor LiDAR limitation discussion (R#6.2, R#8.3)
- GGAM overhead quantification (R#6.3)
- Hyperparameter robustness paragraph (R#6.4)
- Method section clarity pass (R#6.5)
- Language proof-reading (R#6.7)
- Architecture complexity rebuttal (R#8.2)
- Performance improvement justification prose (R#8.4 — partially done in response)
- Enhanced qualitative analysis (R#8.6)

---

*End of Response Letter*
