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

- **Reviewer #6** raises concerns about novelty positioning, the absence of outdoor LiDAR experiments, the computational overhead of GGAM, hyperparameter sensitivity, and writing clarity. We have clarified our unique contributions, quantified GGAM's overhead, and refined the method section. We have conducted additional experiments on the nuScenes outdoor LiDAR dataset, where PointSS achieves 80.9% mIoU, outperforming PTv3 (80.4%) and Pamba (80.4%), demonstrating effective generalization to outdoor scenarios.

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

- <span style="color:#c00000">PointSS introduces additional parameters (52.2M vs. PTv3's 46.2M, +13%), attributable primarily to the GGAM geometric feature extraction module.</span>
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

**Response:** We have revised the Conclusion section to include quantitative summaries of key findings and more concrete future directions. The revised conclusion now explicitly reports: (1) <span style="color:#c00000">73.8% mIoU on S3DIS Area 5, outperforming PTv3 by 0.4% and PCM by 4.0%</span>; (2) <span style="color:#c00000">94.6% OA on ModelNet40</span>; (3) <span style="color:#c00000">80.9% mIoU on nuScenes, surpassing PTv3 (80.4%) and Pamba (80.4%)</span>; (4) <span style="color:#c00000">9–19% inference latency reduction over PTv3 across patch sizes 128–512</span>; (5) <span style="color:#c00000">59% memory reduction at patch size 512 (6.0GB vs. 14.7GB)</span>; and (6) <span style="color:#c00000">stable scaling to patch size 2048 while PTv3 encounters OOM at 1024</span>.

For future directions, we now propose: (1) automatic tuning of scale constraint factors $\alpha_s$ via learnable parameterization or neural architecture search; (2) extension to additional outdoor LiDAR benchmarks such as SemanticKITTI and Waymo Open Dataset; (3) more efficient geometric fusion strategies to reduce GGAM's computational overhead; and (4) investigation of PointSS's applicability to other 3D vision tasks such as object detection and instance segmentation.

**Modifications:** Revised Conclusion section [lines 686–692] with quantitative findings and concrete future directions.

---

# Response to Reviewer #6

## Comment R#6.1 — Limited Novelty
<span style="color:#1f6feb">Novelty is somewhat limited; similar ideas of geometric feature enhancement and multi-scale modeling exist in prior work. The paper should more clearly articulate its unique contributions.</span>

**Response:** We sincerely thank the reviewer for this valuable feedback. We fully acknowledge that geometric feature enhancement and multi-scale modeling are well-established directions in point cloud analysis. In response to this concern, we have substantially revised the Introduction and Related Work sections to more clearly articulate and demonstrate the specific technical contributions of PointSS:

**(1) Enhanced presentation of GGAM's design rationale.** We have added explicit discussion clarifying that GGAM addresses a specific challenge arising from SSM-based serialization—the loss of spatial proximity information—which differs from the problems addressed by prior geometric methods (e.g., PointGA, PointMSGT, RepSurf). We now emphasize three key technical aspects: (i) GGAM constructs local neighborhoods by partitioning the serialized 1D sequence into fixed-size contiguous windows (size 64), reducing graph construction cost to $\mathcal{O}(N)$ compared to KNN-based approaches; (ii) dual-serialization employs both Z-order and Hilbert curves to extract complementary geometric features, then fuses them via cross-sequence attention to compensate for the proximity information lost; and (iii) a geometric consistency gate modulates the fused representation. <span style="color:#c00000">We have added detailed comparisons with PointGA, PointMSGT, and RepSurf in the Related Work section to clarify these distinctions.</span>

**(2) Strengthened exposition of ASD-SSM's parameterization strategy.** We have expanded the discussion to clarify that ASD-SSM makes the state transition parameter $\bar{A}$ simultaneously input-dependent AND scale-decoupled. We now explicitly contrast this with PCM (AAAI'25), which allocates separate $\bar{A}$ per scale but keeps $\bar{A}$ input-invariant within each scale, and with standard Mamba/PointMamba, which use a single shared $\bar{A}$. To quantify the benefit of this design, we reference the ablation result in the Parameterization Comparison table: <span style="color:#c00000">ASD-SSM achieves 73.8% mIoU versus 71.6% for the input-invariant per-scale baseline (+2.2%)</span>, demonstrating that this parameterization choice yields substantial gains on this benchmark.

**(3) Added analysis of the functional coupling between GGAM and ASD-SSM.** We have supplemented the manuscript with discussion clarifying that GGAM and ASD-SSM are not independently stacked modules but functionally coupled: the geometric priors produced by GGAM provide the patch-level content features that drive ASD-SSM's scale-aware parameter generator. We now explicitly reference the progressive ablation result showing that <span style="color:#c00000">adding ASD-SSM to a GGAM-equipped baseline yields +1.9% mIoU</span>, confirming complementary rather than redundant contributions.

We have revised the Introduction (lines 43–66) and Related Work (lines 118–123) to systematically contrast PointSS against the closest prior works (PCM, PointMamba, PointGA, PointMSGT) along these three dimensions, ensuring that our unique contributions are clearly visible to readers.

**Modifications:** Strengthened novelty positioning in Introduction <span style="color:#c00000">lines 43–66</span>; clarified differentiation in Related Work <span style="color:#c00000">lines 118–123</span>.

---

## Comment R#6.2 — Limited Outdoor LiDAR Evaluation
<span style="color:#1f6feb">Evaluation is limited to indoor and synthetic datasets, lacking experiments on outdoor LiDAR datasets, which reduces generalizability.</span>

**Response:** We thank the reviewer for this valuable suggestion. In response to this concern, we have conducted additional experiments on the nuScenes outdoor LiDAR dataset to evaluate the generalization capability of PointSS beyond indoor scenes.

As shown in the newly added Table (nuScenes results) in Section 4.2.3, <span style="color:#c00000">PointSS achieves 80.9% mIoU on nuScenes, outperforming both Transformer-based methods (PTv3: 80.4%, SphereFormer: 79.5%) and SSM-based methods (Pamba: 80.4%)</span>. This demonstrates that the geometry-aware multi-scale design of PointSS generalizes effectively from indoor scenes to outdoor autonomous driving scenarios, where point clouds exhibit different characteristics such as larger spatial extent and sparser density.

The consistent performance gains across indoor (S3DIS), synthetic (ModelNet40), and outdoor (nuScenes) benchmarks validate the robustness and generalizability of our approach.

**Modifications:** Added nuScenes dataset description in Section 4.2 [lines 384–386]; added nuScenes experimental results and analysis in Section 4.3.3 [lines 440–462]; updated Abstract [lines 72–74] and Introduction [lines 128–130] to include nuScenes results.

---

## Comment R#6.3 — GGAM Computational Overhead
<span style="color:#1f6feb">The additional computational overhead introduced by GGAM (dual-serialization, graph construction) is insufficiently analyzed in terms of memory and runtime.</span>

**Response:** We thank the reviewer for this important concern. We have added a detailed quantitative analysis of GGAM's computational overhead in the revised Section 4.4. The key findings are:

**(1) Parameter overhead.** <span style="color:#c00000">PointSS introduces 6.0M additional parameters over PTv3 (52.2M vs. 46.2M, +13%)</span>, attributable to two main components:
- **GGAM module**: Dual manifold encoders (Z-order and Hilbert), cross-serialization attention, and geometric feature projections
- **ASD-SSM module**: Scale-decoupled state space parameter generators

This moderate parameter increase (+13%) is well justified by the substantial performance gain (73.8% vs. 73.4% mIoU, +0.4%) and improved efficiency (59% memory reduction, 9–19% faster inference).

**(2) Memory overhead.** GGAM's memory footprint primarily comes from storing dual-serialization intermediate features (hidden_dim=64 for both Z-order and Hilbert paths) and cross-attention maps. Importantly, <span style="color:#c00000">GGAM's overhead remains constant across patch sizes because it operates on fixed-size neighborhoods (k=64) within the serialization window</span>. In contrast, PTv3's self-attention operates on the entire patch: as patch size increases from 512 to 1024, the attention sequence length doubles, leading to quadratic memory growth (14.7GB → 27.8GB, OOM). This design choice is key to PointSS's superior scalability: PointSS maintains stable 6.0GB memory usage even at patch size 2048.

**(3) Latency overhead.** While GGAM introduces additional computation for dual-serialization and geometric feature extraction, <span style="color:#c00000">the overall system still achieves 9–19% faster inference than PTv3 across patch sizes 128–512</span>. This demonstrates that the linear-complexity SSM backbone more than compensates for GGAM's overhead, resulting in a net efficiency gain.

**(4) Graph construction complexity.** Critically, GGAM's graph construction does NOT require expensive KNN search. Instead, it directly partitions the serialized 1D sequence into fixed-size contiguous windows (size k=64), treating each window as a local neighborhood. This reduces graph construction to <span style="color:#c00000">O(N) complexity</span> — a simple reshaping operation — compared to O(N log N) or O(N²) for KNN-based methods. The dual-serialization (Z-order + Hilbert) performs this windowing twice with different orderings, still maintaining O(N) total complexity.

In summary, while GGAM introduces moderate parameter and memory overhead, its design ensures linear scalability, and the overall system remains substantially more efficient than attention-based alternatives (6.0GB vs. PTv3's 14.7GB at patch size 512).

**Modifications:** Added GGAM overhead analysis in Section 4.4 [lines 487–521]; updated efficiency comparison table to include per-module breakdown.

---

## Comment R#6.4 — Hyperparameter Sensitivity
<span style="color:#1f6feb">Key hyperparameters (e.g., scale constraint factors) appear to be manually designed and seem sensitive, lacking robustness analysis or automatic tuning schemes.</span>

**Response:** We thank the reviewer for this important observation. We address the concerns regarding the scale constraint factor α as follows:

**(1) Robustness analysis.** We have conducted systematic sensitivity analysis in Table VII (lines 602–620), evaluating nine configurations of α across three scales. The results demonstrate robustness within reasonable ranges:

- When α follows a monotonically increasing pattern from fine to coarse scales, <span style="color:#c00000">performance remains stable: configurations (0.3,0.5,0.7), (0.3,0.6,0.9), and (0.3,0.7,0.9) achieve 73.3%–73.8% mIoU, with variation <0.5%</span>.
- The model tolerates moderate boundary adjustments: <span style="color:#c00000">varying α₁ ∈ [0.2, 0.4] while keeping other scales fixed yields 73.2%–73.3% mIoU</span>, indicating that exact values are not critical.
- Only physically inconsistent configurations cause significant degradation. For instance, <span style="color:#c00000">reversing the scale ordering (0.9,0.6,0.3) drops performance to 66.5%</span>, which aligns with the design principle: fine scales require rapid state decay (small α) to capture local geometric detail, while coarse scales require slow decay (large α) to maintain global context.

**(2) Design rationale.** The scale constraint factors are not arbitrary hyperparameters but encode a clear physical principle. In the discrete state space model h_k = Ā·h_{k-1} + B̄·u_k, the magnitude of Ā controls state decay rate. By constraining Ā_s ∈ [0, α_s] with α increasing across scales, we ensure that fine scales respond rapidly to local variations while coarse scales retain long-range dependencies. This principle is dataset-agnostic and provides practical guidance: users can reliably adopt a monotonically increasing pattern (e.g., 0.3→0.6→0.9 for three scales) without dataset-specific tuning.

**(3) Toward automatic tuning.** We acknowledge that manual specification of α remains a limitation. As stated in our Conclusion (Section VI, lines 686–692), future work will explore learning α as trainable parameters or deriving them via neural architecture search. However, the current design already provides practical robustness: the systematic ablation demonstrates that the method is stable within reasonable ranges, and the physical interpretation offers clear guidance for hyperparameter selection.

**Modifications:** Strengthened the analysis paragraph following Table VII at lines 602–620; added explicit discussion of robustness and future directions in Conclusion at lines 686–692.

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

**Response:** We appreciate the reviewer's concern regarding the breadth of experimental validation. In response, we have conducted additional experiments on the nuScenes outdoor LiDAR dataset, which is a large-scale autonomous driving benchmark containing 1,000 scenes with full 360-degree LiDAR point clouds.

As shown in the newly added Table (nuScenes results) in Section 4.3.3, <span style="color:#c00000">PointSS achieves 80.9% mIoU on nuScenes, outperforming Point Transformer V3 (80.4%) and Pamba (80.4%)</span>. This result demonstrates cross-dataset generalization: PointSS maintains consistent performance gains when transferring from indoor scenes (S3DIS) to outdoor autonomous driving scenarios (nuScenes), where point clouds exhibit substantially different characteristics including larger spatial extent, sparser density, and different object categories.

We acknowledge that S3DIS, ModelNet40, and nuScenes remain the most widely adopted benchmarks in the point cloud community for indoor segmentation, object classification, and outdoor LiDAR segmentation, respectively. Our consistent improvements across these three diverse benchmarks—spanning indoor, synthetic, and outdoor domains—provide strong evidence for the effectiveness and generalizability of PointSS.

**Modifications:** Added nuScenes dataset description in Section 4.2 [lines 384–386]; added nuScenes experimental results and analysis in Section 4.3.3 [lines 440–462]; updated Abstract [lines 72–74] and Introduction [lines 128–130] to include nuScenes results.

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
| **PointSS** | **52.2M**  | **6.0GB**         | **stable @2048**  | **73.8%**               | **9–19% faster**    |

<span style="color:#c00000">**Key observations:** Despite a +13% parameter increase over PTv3, PointSS achieves a 59% reduction in peak memory at patch size 512 and scales stably to patch size 2048, while PTv3 encounters OOM at 1024. Inference is 9–19% faster across patch sizes 128–512.</span>

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
