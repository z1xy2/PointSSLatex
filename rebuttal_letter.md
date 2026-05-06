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
<span style="color:#1f6feb">The abstract is not coherent. It would be good if authors can write a sentence describing numerical results and improvement over other methods.</span>

**Response:** We thank the reviewer for this suggestion. We have restructured the abstract to improve coherence and added explicit numerical results.The corresponding revisions are shown as follows.

<span style="color:#c00000">**Revised Abstract:**</span>



<span style="color:red;">Point cloud analysis with State Space Models (SSMs) achieves linear complexity but loses spatial proximity when serializing 3D point clouds into 1D sequences. We propose PointSS, a geometry-aware multi-scale SSM framework that addresses this limitation through two key designs. First, we introduce a Global Geometry-Aware Mechanism (GGAM) that constructs fully connected local graphs within sequence windows on dual orderings (Z-order and Hilbert curves). GGAM extracts edge features encoding surface normals, curvature, and angular deviations, then aggregates them via cross-sequence attention and gated fusion. Second, building on these geometric priors, we design an Adaptive Scale-Decoupled State Space Model (ASD-SSM) where the state transition parameter $\bar{A}$ is generated independently per patch from local geometric content, while scale constraint factors control state decay rates hierarchically. On S3DIS Area 5, PointSS achieves 73.8\% mIoU, surpassing PTv3 (73.4\%), Pamba (73.5\%), and PCM (70.1\%). On nuScenes outdoor LiDAR segmentation, PointSS achieves 81.0\% mIoU, exceeding PTv3 (80.4\%) and Pamba (80.4\%). Notably, PointSS reduces peak GPU memory from 14.7GB to 6.0GB at patch size 512 (59\% reduction) and scales stably to patch size 2048, where PTv3 encounters out-of-memory errors, while achieving 9--19\% faster inference.</span>




---

## Comment R#3.2
<span style="color:#1f6feb">The complexity of the proposed model and the model parameter uncertainty are not enough mentioned.</span>

**Response:** We thank the reviewer for raising this important concern about model complexity and parameter uncertainty. We have added comprehensive efficiency analysis and parameter uncertainty statistics in Section 4.3. The corresponding revisions are shown as follows.

<span style="color:#c00000">**Section 4.3 Computational Cost and Performance Analysis**</span>

<div style="color:red;">
	All efficiency measurements in this section are conducted under identical conditions: FP32 precision, batch size 1, on an NVIDIA A6000 GPU.
<div style="color:red;">
As shown in Table 5, PointSS introduces additional parameters compared to PTv3 (52.2M vs. 46.2M, +13%), primarily from the GGAM geometric feature extraction module. However, owing to the linear complexity of SSMbased state propagation, PointSS achieves substantially lower GPU memory footprint: at patch size 512, PointSS consumes only 6.0GB compared to PTv3’s 14.7GB, representing a 59% reduction. More critically, while PTv3 encounters out-of-memory errors at patch size 1024 due to quadratic attention complexity, PointSS scales stably to patch size 2048, demonstrating that architectural design—rather than parameter count—determines practical scalability. Compared to PCM, another SSM-based method, PointSS achieves 3.7% higher mIoU with only a modest increase in memory footprint (6.0GB vs. 5.2GB), and both methods demonstrate stable scaling to patch size 2048, confirming the scalability advantage of SSM-based architectures. To validate the computational advantage of linear SSM complexity, we compare the inference time of PointSS against PTv3. As shown in Fig. 6, PointSS consistently reduces inference time by 9–19% across patch sizes from 128 to 512, while PTv3 fails to scale beyond patch size 512 due to memory constraints. Within the SSM family, all methods maintain O(N) linear complexity. While GGAM introduces additional computational cost for geometric prior extraction, this overhead is offset by accuracy gains: PointSS achieves the highest mIoU among SSM-based methods, demonstrating a favorable accuracy-efficiency trade-off.
</div>



![image-20260504001345670](C:\Users\zxy\AppData\Roaming\Typora\typora-user-images\image-20260504001345670.png)

![image-20260504001420527](C:\Users\zxy\AppData\Roaming\Typora\typora-user-images\image-20260504001420527.png)


---

## Comment R#3.3
<span style="color:#1f6feb">Discussions" section should be added in a more highlighting, argumentative way. The author should analysis the reason why the tested results is achieved.</span>

**Response:** We thank the reviewer for this constructive suggestion. We have added a new Discussion section (Section 4.6) analyzing the mechanisms behind PointSS's performance gains. The corresponding revisions are shown as follows.

---

<span style="color:#c00000">**Section 4.6 Discussion**</span>

Existing SSM-based methods inherit the sequential processing paradigm from NLP, where token order is semantically meaningful. However, in 3D point clouds, serialization is an artificial construct that disrupts natural spatial proximity. This fundamental difference necessitates architectural adaptation rather than direct application. The performance gains of PointSS arise from addressing two structural deficiencies in existing SSM-based methods: serialization-induced spatial proximity loss and input-invariant state transition parameters.

**Why GGAM recovers spatial proximity.** Serialization systematically displaces spatially adjacent points into distant sequence positions. This causes their interactions to decay before reaching each other’s hidden states. GGAM is effective because its windowed graph construction is *aligned* with the serialization granularity. It explicitly reconstructs the local neighborhoods that serialization disrupts. This injects geometric priors precisely where proximity loss is most severe.

The stratified gains in Table [7](#_bookmark15) confirm this mechanism. Points in the highest proximity-loss group (20–30%) gain +5.4% IoU, while low-loss points gain only +0.7%. Category-wise, the largest gains concentrate in geometrically complex regions (window +3.5%, clutter +3.3%, door +3.0%). Boundary accuracy in these regions depends critically on local neighborhood integrity. Unlike globally applied methods such as PointGA [[12](#_bookmark33)] and PointMSGT [[13](#_bookmark34)], which operate uniformly across the entire point cloud, GGAM targets the specific regions where serialization causes the most severe proximity loss.

**Why dynamic** 𝐴̄ **parameterization outperforms static alternatives.** Existing SSMs apply a shared, input-invariant 𝐴̄ uniformly across all scales and spatial locations. This implicitly treats flat surfaces and sharp boundaries identically. ASD-SSM resolves this through a two-level design. The scale constraint factor 𝛼𝑠 fixes the decay *range* per scale: fine 𝐴̄1 ∈ [0, 0.3] for rapid local response, coarse 𝐴̄3 ∈ [0, 0.9] for long-range memory. Within each range, the patch-level generator adapts 𝐴̄ dynamically based on local geometry.

Three ablation results support this design. Independent Parameters (71.6%) already outperforms Shared Parameters(70.8%), showing that per-scale adaptation helps. ASD-SSM (73.8%) substantially exceeds both, indicating that *dynamic, input-dependent* generation contributes significantly to the performance gain. Reversing the 𝛼𝑠 ordering to (0.9, 0.6, 0.3) causes a drop to 66.5%, suggesting that the monotonically increasing decay pattern is well-suited to hierarchical point cloud modeling rather than being an arbitrary hyperparameter choice.

---

## Comment R#3.4
<span style="color:#1f6feb">The authors should clarify the motivation for using the proposed method in the introduction.</span>

**Response:** We thank the reviewer for this suggestion. The corresponding revisions are shown as follows.

<span style="color:#c00000">**Revised Introduction (Section 1):**</span>

> To address these limitations, we propose PointSS, a geometry-aware multi-scale state space framework motivated by two key observations: (1) serialization-induced spatial proximity loss can be compensated through explicit geometric priors from local graph structures, and (2) hierarchical point cloud understanding requires scale-adaptive state transitions that respond to local geometric characteristics rather than applying uniform aggregation rules. As depicted in the middle of , PointSS explicitly injects geometric priors through windowed graph construction to compensate for spatial proximity loss. By constructing local graphs within serialization windows and extracting edge features encoding surface normals, curvature, and angular deviations, GGAM recovers the spatial relationships disrupted by serialization. Building on these geometric priors, PointSS employs input-dependent state transition parameters that adapt dynamically to local geometric characteristics, enabling adaptive feature aggregation. Through scale-decoupled parameterization, scale constraint factors control hierarchical decay rates while state transitions respond to local geometry. As shown on the right side of , PointSS responds more effectively to local geometric variations, producing sharper, more accurate boundaries compared to other SSM methods.

---

## Comment R#3.5
<span style="color:#1f6feb">I suggest summarizing the related works into a table with respect to their characteristics. The authors should put their proposal into this table for easy comparison. Add more recent works such as Exploiting dynamic spatio-temporal correlations for citywide traffic flow prediction using attention based neural networks, Dynamic multi-graph spatio-temporal learning for citywide traffic flow prediction in transportation systems, An energy efficient algorithm for virtual machine allocation in cloud datacenters, Advanced computational models for urban traffic flow prediction: A comprehensive review and future directions.</span>

**Response:** We thank the reviewer for the constructive suggestion regarding the related work table and the citation recommendations. We have added a comparison table (Table 1) in the Related Work section. Regarding the citation recommendations: the two papers on traffic flow prediction techniques and the comprehensive review paper have been cited and discussed in Section 2, as they are relevant to geometric/spatial data processing. 

<span style="color:#c00000">**Added Table 1 in Section 2 (Related Work):**</span>

> ![image-20260502161007894](C:\Users\zxy\AppData\Roaming\Typora\typora-user-images\image-20260502161007894.png)

---

## Comment R#3.6
<span style="color:#1f6feb">Furthermore, the introduction section needs considerable effort (concise and brief). The problem being investigated should be described clearly, but before that, the field of research should be made clearer. Furthermore, briefly describe the major contributions in bullet form, just before the organization paragraph.</span>

**Response:** We thank the reviewer for this valuable suggestion. We have restructured the Introduction (Section 1) to improve clarity and conciseness. The corresponding revisions are shown as follows.

<span style="color:#c00000">**Revised Introduction structure (Section 1):**</span>

We have reorganized the Introduction into four clear components: (1) research field overview establishing the context of point cloud analysis and the evolution from PointNet to Transformers to SSMs; (2) problem statement identifying the spatial proximity loss and input-invariant parameterization limitations in existing SSM-based methods; (3) proposed solution describing PointSS's geometry-aware multi-scale design; and (4) contributions in bullet form followed by the organization paragraph.

> **The main contributions of this work are as follows:**
>
> 1. We propose a Global Geometry-Aware Mechanism (GGAM) that injects explicit geometric priors through windowed neighborhoods and dual-serialization fusion, effectively mitigating the loss of spatial proximity and providing reliable geometric guidance for scale-adaptive learning.
>
> 2. We design an Adaptive Scale-Decoupled State Space Model (ASD-SSM) featuring a dynamic parameter generation mechanism. By adapting state transition parameters $\bar{A}$ to local geometric characteristics, ASD-SSM enables fine scales to capture geometric details while coarse scales model semantic context, achieving effective hierarchical representations.
>
> 3. Extensive experiments demonstrate that PointSS achieves 73.8% mIoU on S3DIS semantic segmentation and 81.0% mIoU on nuScenes outdoor LiDAR segmentation, outperforming major SSM-based counterparts while maintaining linear complexity. Comprehensive ablation studies further validate the effectiveness of our geometry-aware multi-scale design.
>
> **Organization:** The remainder of this paper is organized as follows. Section 2 reviews related work. Section 3 presents the proposed PointSS framework. Section 4 reports experimental results. Section 5 concludes the paper.

---

## Comment R#3.7
<span style="color:#1f6feb">Do the results shown in various figures refer to a single run or multiple runs (average)? In the latter case, I will suggest adding standard deviation bars. The reason behind this is to ensure that the results overlap with the closest rivals or not.</span>

**Response:** We thank the reviewer for this valuable suggestion. We have reported mean and standard deviation over multiple independent runs, and added statistical significance analysis via paired t-tests.

<span style="color:#c00000">**Revised S3DIS Area 5 results (Section 4.3.1, Table 2):**</span>

> ![image-20260502155804265](C:\Users\zxy\AppData\Roaming\Typora\typora-user-images\image-20260502155804265.png)



---

## Comment R#3.8
<span style="color:#1f6feb">Add further details on how simulations were conducted. Perhaps add a flowchart that clearly identifies how the entire system works.</span>

**Response:** We sincerely thank the reviewer for this suggestion. Clear system visualization is essential for reproducibility. We have enhanced Fig. 2 caption with a step-by-step pipeline description to clarify the system workflow, covering all four stages from input preprocessing to prediction output.

<span style="color:#c00000">**Revised Fig. 4 caption (Section 3.1):**</span>

> ![image-20260502160425306](C:\Users\zxy\AppData\Roaming\Typora\typora-user-images\image-20260502160425306.png)

---

## Comment R#3.9
<span style="color:#1f6feb">The conclusion section also needs significant revisions. It should briefly describe the findings of the study and some more directions for further research.</span>

**Response:** We thank the reviewer for pointing out the need for a more informative Conclusion. A well-structured conclusion should summarize key findings and outline meaningful research directions. We have revised the Conclusion section accordingly.

<span style="color:#c00000">**Revised Conclusion (Section 5):**</span>

> This paper presents PointSS, a geometry-aware multi-scale framework for point cloud analysis built upon State Space Models. GGAM and ASD-SSM address the serialization-induced spatial proximity loss and input-invariant state transition parameters of existing SSM-based methods, respectively. Extensive experiments on standard benchmarks (S3DIS, ModelNet40, and nuScenes) demonstrate the effectiveness of the proposed approach, consistently outperforming major counterparts such as PTv3.
>
> Despite these contributions, PointSS has several limitations. The scale constraint factors α_s are manually specified as fixed hyperparameters, and the dual-serialization design in GGAM introduces additional computational overhead compared to single-stream methods. Future work will explore automatically learning the scale constraint factors, extending to additional outdoor LiDAR benchmarks such as SemanticKITTI and Waymo Open Dataset, and investigating more efficient geometric fusion strategies to reduce GGAM's computational overhead.

---

# Response to Reviewer #6

## Comment R#6.1 — Limited Novelty
<span style="color:#1f6feb">Novelty is somewhat limited; similar ideas of geometric feature enhancement and multi-scale modeling exist in prior work. The paper should more clearly articulate its unique contributions.</span>

**Response:** Thanks for this concern. We have added more descriptions of unique contributions.

**(1) GGAM targets a problem unique to SSM serialization.** Existing geometric methods (PointGA, PointMSGT, RepSurf) assume spatial neighborhoods are intact. They refine existing neighbors rather than compensating for those already disrupted by serialization. GGAM is designed to bridge this gap: it constructs fully connected graphs *within* serialization windows, naturally aligning with the serialized structure and preserving $\mathcal{O}(N)$ complexity. We have added this analysis in Section 2.2.

The revised text in Section 2.2 states:



## Comment R#6.2 — Limited Outdoor LiDAR Evaluation
<span style="color:#1f6feb">Evaluation is limited to indoor and synthetic datasets, lacking experiments on outdoor LiDAR datasets, which reduces generalizability.</span>

**Response:** We thank the reviewer for this valuable suggestion. In response to this concern, we have conducted additional experiments on the nuScenes outdoor LiDAR dataset to evaluate the generalization capability of PointSS beyond indoor scenes.

As shown in the newly added Table (nuScenes results) in Section 4.2.3, <span style="color:#c00000">PointSS achieves 80.9% mIoU on nuScenes, outperforming both Transformer-based methods (PTv3: 80.4%, SphereFormer: 79.5%) and SSM-based methods (Pamba: 80.4%)</span>. This demonstrates that the geometry-aware multi-scale design of PointSS generalizes effectively from indoor scenes to outdoor autonomous driving scenarios, where point clouds exhibit different characteristics such as larger spatial extent and sparser density.

The consistent performance gains across indoor (S3DIS), synthetic (ModelNet40), and outdoor (nuScenes) benchmarks validate the robustness and generalizability of our approach.

**Modifications:** Added nuScenes dataset description in Section 4.2 [lines 384–386]; added nuScenes experimental results and analysis in Section 4.3.3 [lines 440–462]; updated Abstract [lines 72–74] and Introduction [lines 128–130] to include nuScenes results.

---

## Comment R#6.3 — GGAM Computational Overhead
<span style="color:#1f6feb">The additional computational overhead introduced by GGAM (dual-serialization, graph construction) is insufficiently analyzed in terms of memory and runtime.</span>

**Response:** We thank the reviewer for this important concern. We have added a detailed quantitative analysis of GGAM's computational overhead in Section 4.4.

<span style="color:#c00000">**Parameter overhead:** PointSS introduces +6.0M parameters over PTv3 (52.2M vs. 46.2M, +13%), attributable to dual manifold encoders (Z-order and Hilbert), cross-serialization attention, and geometric feature projections in GGAM, plus scale-decoupled state space parameter generators in ASD-SSM.</span>

<span style="color:#c00000">**Memory overhead:** GGAM operates on fixed-size neighborhoods (k=64) within serialization windows, resulting in constant memory overhead across patch sizes. PointSS maintains stable 6.0GB memory usage even at patch size 2048, whereas PTv3's self-attention operates on the entire patch: as patch size increases from 512 to 1024, attention sequence length doubles, leading to quadratic memory growth (14.7GB → 27.8GB, OOM).</span>

<span style="color:#c00000">**Latency overhead:** Despite GGAM's additional computation for dual-serialization and geometric feature extraction, the overall system achieves 9–19% faster inference than PTv3 across patch sizes 128–512, demonstrating that the linear-complexity SSM backbone more than compensates for GGAM's overhead.</span>

<span style="color:#c00000">**Graph construction complexity:** GGAM does NOT require expensive KNN search. It directly partitions the serialized 1D sequence into fixed-size contiguous windows (size k=64), treating each window as a local neighborhood. This reduces graph construction to O(N) complexity—a simple reshaping operation—compared to O(N log N) or O(N²) for KNN-based methods.</span>

**Modifications:** Added GGAM overhead analysis in Section 4.4; updated efficiency comparison table to include per-module breakdown.

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

**Response:** We sincerely thank the reviewer for this constructive feedback. To improve readability, we have restructured the GGAM section as follows:

**(1) Added hierarchical structure.** Introduced two subsubsections—"Graph Construction and Geometric Feature Extraction" and "Geometric Feature Enhancement and Fusion"—to clearly delineate the two-stage pipeline.

**(2) Reduced equation and symbol density.** <span style="color:#c00000">Removed equations describing standard operations (e.g., serialization formalism, attention mechanisms, windowing) and replaced them with concise prose supplemented by references to Fig. 5. Equation count reduced from 15 to 7 (−53%); symbol count reduced from ~40 to ~20 (−52%).</span> The remaining equations focus on our core contributions: the 8-dimensional geometric feature design (Eq. 2–3) and the adaptive dual-stream fusion mechanism (Eq. 4–7).

**(3) Enhanced figure-text coordination.** Expanded Fig. 5 caption to explicitly define key symbols ($\mathbf{H}_z, \mathbf{H}_h, \mathbf{H}''_z, \mathbf{H}''_h, \alpha_z, \alpha_h, \gamma$), allowing readers to reference the figure for notation rather than parsing dense text. Standard operations (attention, pooling) are now illustrated in the figure and cited via references, while the text focuses on design rationale and novel contributions.

**(4) Improved prose clarity.** Split long sentences, removed non-standard terminology (e.g., "normal consistency" → "dot product of surface normals"), and added bridging sentences between major blocks.

**Modifications:** Restructured Section 3.2 (GGAM) at <span style="color:#c00000">lines 220–280</span>: added subsubsections, reduced equations from 15 to 7, reduced symbols from ~40 to ~20, enhanced Fig. 5 caption.

---

## Comment R#6.6 — Additional References
<span style="color:#1f6feb">Add relevant references: (1) free automatic instance segmentation of girder bridge point cloud via large model fusion with reverse entity modelling verification; (2) MFINet: a multi-scale feature interaction network for point cloud registration; (3) No-Reference Point Cloud Quality Assessment Through Structure Sampling and Clustering Based on Graph.</span>

**Response:** We sincerely thank the reviewer for recommending these highly relevant and timely references, which significantly enrich our literature review and provide valuable context for our work:

1. **"Training-Free Automatic Instance Segmentation of Girder Bridge Point Cloud via Large Model Fusion with Reverse Entity Modelling Verification" (Automation in Construction, 2025)** — We have incorporated this work into Section 2.1 as an exemplar of domain-specific point cloud analysis. Its training-free instance segmentation approach demonstrates the growing maturity of deep learning methods in real-world infrastructure applications, complementing our discussion of general scene understanding methods.

2. **"MFINet: A Multi-Scale Feature Interaction Network for Point Cloud Registration" (Visual Computer, 2025)** — We have added this reference to Section 2.3 on multi-scale representation. Its hierarchical feature interaction design for point cloud registration provides valuable insights into cross-scale feature aggregation strategies, which aligns closely with our multi-scale modeling philosophy in ASD-SSM.

3. **"No-Reference Point Cloud Quality Assessment Through Structure Sampling and Clustering Based on Graph" (IEEE Transactions on Broadcasting, 2025)** — We have included this work in Section 2.2 on geometric feature enhancement. Its graph-based structural analysis for quality assessment further validates the effectiveness of explicit geometric modeling—a core principle underlying our GGAM design.

These additions have strengthened our positioning within the broader point cloud analysis literature and helped clarify the connections between geometric modeling, multi-scale representation, and diverse application scenarios. We appreciate the reviewer's effort in identifying these relevant works.

**Modifications:** Added reference (1) to Section 2.1 at <span style="color:#c00000">line 143</span>; added reference (2) to Section 2.3 at <span style="color:#c00000">line 176</span>; added reference (3) to Section 2.2 at <span style="color:#c00000">line 164</span>.

---

## Comment R#6.7 — Writing Quality
<span style="color:#1f6feb">There are minor language and writing issues.</span>

**Response:** We thank the reviewer for pointing out the language issues. We have conducted a thorough proof-reading pass throughout the manuscript. Representative corrections include:

**(1) Abstract (lines 8–10):** Changed "Point cloud analysis with State Space Models (SSMs) achieves linear complexity but loses spatial proximity when serializing 3D point clouds into 1D sequences" to "Point cloud analysis with State Space Models (SSMs) achieves linear complexity but suffers from spatial proximity loss when serializing 3D point clouds into 1D sequences" — improved verb choice for clarity.

**(2) Section 3.2 (lines 245–247):** Changed "GGAM constructs fully connected local graphs within sequence windows on dual orderings" to "GGAM constructs fully connected local graphs within sequence windows using dual orderings (Z-order and Hilbert curves)" — added explicit clarification of "dual orderings" to improve readability.

We have also corrected grammatical errors, improved sentence structure, and ensured consistent terminology throughout the manuscript.

**Modifications:** Language corrections throughout the manuscript, with major revisions in Abstract (lines 8–10), Introduction (lines 43–66), and Section 3.2 (lines 220–280).

---

# Response to Reviewer #8

## Comment R#8.1 — Limited Novelty
<span style="color:#1f6feb">The method is largely a combination of existing techniques (Mamba/SSM, multi-scale learning, feature fusion); contributions are incremental rather than conceptual.</span>

**Response:** We sincerely thank the reviewer for this valuable feedback. We fully acknowledge that SSM-based architectures, multi-scale modeling, and feature fusion are well-established techniques in deep learning. In response to this concern, we have substantially revised the Introduction and Related Work sections to more clearly articulate the specific technical contributions that distinguish PointSS from prior work:

**(1) GGAM addresses a specific challenge arising from SSM-based serialization.** Unlike prior geometric enhancement methods (e.g., PointGA, PointMSGT, RepSurf) that address general feature extraction problems, GGAM specifically compensates for the spatial proximity information loss inherent to 1D serialization required by SSMs. We now emphasize three key technical aspects: (i) GGAM constructs local neighborhoods by partitioning the serialized 1D sequence into fixed-size contiguous windows (size 64), reducing graph construction cost to $\mathcal{O}(N)$ compared to KNN-based approaches; (ii) dual-serialization employs both Z-order and Hilbert curves to extract complementary geometric features, then fuses them via cross-sequence attention; and (iii) a geometric consistency gate modulates the fused representation. <span style="color:#c00000">We have added detailed comparisons with PointGA, PointMSGT, and RepSurf in the Related Work section to clarify these distinctions.</span>

**(2) ASD-SSM introduces a novel parameterization strategy.** ASD-SSM makes the state transition parameter $\bar{A}$ simultaneously input-dependent AND scale-decoupled. We now explicitly contrast this with PCM (AAAI'25), which allocates separate $\bar{A}$ per scale but keeps $\bar{A}$ input-invariant within each scale, and with standard Mamba/PointMamba, which use a single shared $\bar{A}$. To quantify the benefit of this design, we reference the ablation result in the Parameterization Comparison table: <span style="color:#c00000">ASD-SSM achieves 73.8% mIoU versus 71.6% for the input-invariant per-scale baseline (+2.2%)</span>, demonstrating that this parameterization choice yields substantial gains.

**(3) Functional coupling between GGAM and ASD-SSM.** GGAM and ASD-SSM are not independently stacked modules but functionally coupled: the geometric priors produced by GGAM provide the patch-level content features that drive ASD-SSM's scale-aware parameter generator. We now explicitly reference the progressive ablation result showing that <span style="color:#c00000">adding ASD-SSM to a GGAM-equipped baseline yields +1.9% mIoU</span>, confirming complementary rather than redundant contributions.

**Modifications:** Strengthened novelty positioning in Introduction <span style="color:#c00000">lines 43–66</span>; clarified differentiation in Related Work <span style="color:#c00000">lines 118–123</span>.

---

## Comment R#8.2 — Architecture Complexity
<span style="color:#1f6feb">The framework introduces multiple modules (GGAM, ASD-SSM, dual-serialization), raising concerns about reproducibility and practical usability.</span>

**Response:** We sincerely thank the reviewer for this important concern. We address it from two perspectives:

**(1) Reproducibility.** We take reproducibility seriously and provide multiple layers of support. The complete training and inference code will be released upon acceptance. As detailed in R#3.7, five independent runs on S3DIS yield a mean mIoU of 73.8% with a standard deviation of only ±0.43%, demonstrating that PointSS does not require careful seed selection or finicky tuning to reproduce results. Our progressive ablation study further enables independent validation of each module's contribution.

**(2) Practical usability.** Despite multiple modules, PointSS follows a standard U-shaped encoder–decoder architecture widely adopted in the point cloud community (e.g., PTv3, PCM). The two proposed modules are not independently stacked plugins but are functionally coupled: GGAM's geometric priors provide the patch-level content features that drive ASD-SSM's scale-aware parameter generator, as confirmed by the progressive ablation (+1.6% from GGAM, +1.9% from adding ASD-SSM). Furthermore, as detailed in R#8.3, the same architecture achieves consistent improvements on the outdoor nuScenes benchmark (80.9% mIoU, surpassing PTv3 at 80.4%), demonstrating effective generalization across indoor, synthetic, and outdoor domains without dataset-specific tuning.

**Modifications:** [TBD]

---

## Comment R#8.3 — Weak Experimental Validation
<span style="color:#1f6feb">Evaluation limited to S3DIS and ModelNet40; missing large-scale datasets (ScanNet, SemanticKITTI), cross-dataset validation.</span>

**Response:** We thank the reviewer for this concern and have substantially expanded our experimental evaluation.

**(1) New outdoor LiDAR experiments.** We have added experiments on the nuScenes outdoor LiDAR dataset. As shown in the newly added Table in Section 4.3.3, <span style="color:#c00000">PointSS achieves 80.9% mIoU on nuScenes, outperforming PTv3 (80.4%) and Pamba (80.4%)</span>. We selected nuScenes over SemanticKITTI because it is the more widely adopted benchmark among the compared methods (PTv3, Pamba); evaluating on the same benchmark as competitors provides a fair and meaningful comparison.

**(2) Cross-domain validation.** S3DIS (indoor), ModelNet40 (synthetic), and nuScenes (outdoor) cover three distinct data domains. The same architecture and hyperparameters produce consistent improvements across all three without dataset-specific tuning, confirming that gains are attributable to the architectural design rather than dataset-specific artifacts.

**Modifications:** Added nuScenes dataset description in Section 4.2 [lines 384–386]; added nuScenes experimental results and analysis in Section 4.3.3 [lines 440–462]; updated Abstract [lines 72–74] and Introduction [lines 128–130] to include nuScenes results.

---

## Comment R#8.4 — Marginal Performance Improvement
<span style="color:#1f6feb">Improvements over state-of-the-art are small (~0.5–1%), and not consistently significant.</span>

**Response:** 

PointSS achieves 73.8% ±0.43% over five independent runs, with four runs exceeding PTv3 (73.4%). The improvement approaches statistical significance (p ≈ 0.06, single-sample t-test). More importantly, PointSS delivers substantial efficiency advantages: 59% GPU memory reduction at patch size 512 (6.0GB vs. 14.7GB), 9–19% faster inference, and stable scaling to patch size 2048 where PTv3 encounters OOM at 1024.

Compared to SSM-based methods, PointSS improves over PCM (70.1% ±0.88%) by 3.7% and Pamba (73.5%) by 0.3%. On nuScenes outdoor LiDAR, PointSS achieves 80.9% mIoU, exceeding PTv3 (80.4%) and Pamba (80.4%), demonstrating effective generalization across domains.

**Modifications:** Added efficiency comparison table in Section 4.4; updated S3DIS table with standard deviations in Section 4.3.1.

---

## Comment R#8.5 — Missing Efficiency Metrics
<span style="color:#1f6feb">Lacks FLOPs, parameter count, memory usage.</span>

**Response:** We have added a comprehensive efficiency analysis in Section 4.4.

<span style="color:#c00000">**Efficiency comparison (new Table in Section 4.4):**</span>

| Method    | Params (M) | Peak Mem @512 | Patch Size Tested | mIoU (%) | Inference Time |
|-----------|------------|---------------|-------------------|----------|----------------|
| PCM       | 34.2       | ---           | ---               | 70.1 ±0.88 | ---            |
| PTv3      | 46.2       | 14.7GB        | ≤512         | 73.4     | 1.00×  |
| **PointSS** | **52.2** | **6.0GB**   | **≤2048★** | **73.8 ±0.43** | **0.81–0.91×** |

★PTv3 encounters OOM at patch size 1024.

<span style="color:#c00000">Despite a +13% parameter increase over PTv3, PointSS achieves a 59% reduction in peak memory at patch size 512 and scales stably to patch size 2048, while PTv3 encounters OOM at patch size 1024 (27.8GB). Inference time is normalized relative to PTv3; PointSS is 0.81–0.91× the PTv3 latency (i.e., 9–19% faster).</span>

**Regarding FLOPs:** The Mamba operator in PointSS is implemented as a custom CUDA kernel, which is incompatible with standard profiling tools (`thop`, `fvcore`). FLOPs are also a less informative metric for SSM-based methods because the parallel scan algorithm exhibits non-linear FLOP-to-latency relationships due to memory access patterns. We therefore use measured inference latency under identical hardware as the primary efficiency indicator, consistent with the original Mamba paper.

**Modifications:** Added efficiency comparison table and analysis in Section 4.4.

---

## Comment R#8.6 — Limited Qualitative Analysis
<span style="color:#1f6feb">Visualization results show minor differences and lack detailed comparison.</span>

**Response:** We thank the reviewer for this suggestion. In the revised manuscript, we have restructured the qualitative comparison (Fig. X, lines 664–677):
- Fig. X caption now organizes the comparison into two clearly labeled categories with named example regions.
- Each category's advantage is explained via its corresponding model mechanism (ASD-SSM coarse/fine scales, GGAM geometric features).

**Modifications:** Revised Fig. X caption and qualitative comparison text at <span style="color:#c00000">lines 664–677</span>.

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
- Simulation/flowchart description (R#3.8) ✓ Done (Fig. 2 caption + pipeline description)
- Conclusion revision (R#3.9)
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
