# Conversation Export: Microarray Normalization & GSE3431

---

## Resources Referenced

### Websites Accessed
- https://www.science.org/doi/10.1126/science.1120499 (attempted, 403 error)
- https://genespring-support.com/support/documentationnew
- https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4868104/
- http://grf.lshtm.ac.uk/genespringprot.htm (attempted, robots.txt blocked)
- https://pmc.ncbi.nlm.nih.gov/articles/PMC1797020/
- https://en.wikipedia.org/wiki/Microarray_analysis_techniques
- https://pmc.ncbi.nlm.nih.gov/articles/PMC1904274/
- https://www.sciencedirect.com/science/article/pii/S1016847823174188
- https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE3431
- https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GPL90&form=text&view=full
- https://www.science.org/doi/10.1126/science.1120499 (search result)
- https://www.biostars.org/p/221440/
- https://www.biostars.org/p/48574/
- https://pmc.ncbi.nlm.nih.gov/articles/PMC3508193/
- https://pubmed.ncbi.nlm.nih.gov/16254148/
- https://www.researchgate.net/publication/7513043_Logic_of_the_Yeast_Metabolic_Cycle_Temporal_Compartmentalization_of_Cellular_Processes

### User-Provided Files
- `excerpt_from_previous_claude_message.txt` (provided multiple times, containing excerpts from earlier responses)
- `tu_som__2_.pdf` — Supporting Online Material for Tu et al. 2005, *Science* 310:1152–1158

---

## Conversation

---

### User
I'm reading the supplemental information for this early 2000s paper: https://www.science.org/doi/10.1126/science.1120499 and it describes the authors normalizing microarray data per chip using Genespring V7. Can you walk me through what this normalization process probably entailed?

### Assistant

These are Affymetrix spike-in and housekeeping control probesets — the `AFFX-` prefix stands for **Affymetrix**, and they were printed on every GeneChip as internal quality controls. They serve several distinct purposes, and the different groups in your data represent different categories.

*(Note: this response was given before the AFFX question below; the GeneSpring normalization question was answered first.)*

GeneSpring V7 had a very specific and well-documented normalization workflow that was extremely common in the early-to-mid 2000s microarray literature. Here's a walkthrough of what the authors almost certainly did.

**The Two-Stage Normalization in GeneSpring V7**

GeneSpring 7's default normalization was a two-step process — "Per Chip" followed by "Per Gene" — though the supplemental focuses on just the first stage.

**Step 1: Per Chip normalization**

This step addresses *between-chip* technical variability. The assumption is that systematic differences in signal intensity exist across arrays due to RNA quantity, labeling efficiency, hybridization conditions, scanner settings, etc. GeneSpring 7's per-chip normalization was a "to median" method — every signal intensity value on a given chip was divided by the median signal intensity of all spots on that chip:

> normalized value = raw intensity / median intensity of all probes on that chip

Every chip's median is brought to 1.0. This assumes that the *median* gene is not truly differentially expressed across samples. The median is used rather than the mean because it is robust to outliers — a handful of highly expressed genes won't skew it.

**What happened before this step?**

Per chip normalization was applied to already-extracted signal intensities, after the array image had been scanned and features extracted. For two-color (cDNA) arrays this followed background subtraction and computation of the Cy5/Cy3 ratio; for single-color Affymetrix chips, it followed probe summarization (often MAS5 or RMA).

**Step 2: Per Gene normalization (often paired with it)**

Per Gene normalization divided each gene's value across all samples by that gene's median value across a reference set of samples. This brought every gene's median to 1.0 across experiments, useful for clustering and visualization, but making the data unsuitable for comparing absolute expression levels.

**Assumptions and limitations**

The Per Chip median normalization assumes most genes are *not* differentially expressed between conditions. Some studies moved toward percentile-based normalization (e.g., dividing by the 75th percentile) to be more robust when expression distributions were skewed. By today's standards this approach is considered fairly naive compared to quantile normalization or RMA, but for the early 2000s it was standard practice.

---

### User
*(Provided data snippet showing the end of a chip data file, including probesets 9980_at through 9999_at and many AFFX- control probesets)*

Can you tell me what the AFFX- features mean and what it is that they represent? Are they somehow used in normalization procedures?

### Assistant

These are Affymetrix spike-in and housekeeping control probesets. The `AFFX-` prefix stands for **Affymetrix**.

**Bacterial spike-in controls (BioB, BioC, BioDn, CreX, DapX, LysX, PheX, ThrX, TrpnX)**

These are the most important QC features. Before hybridization, known quantities of synthetic bacterial RNA transcripts were spiked into every sample at defined concentrations. The genes were chosen because they have no homology to eukaryotic genomes, so any signal must come from the spike-in itself.

They were added in a staircase of concentrations — BioB at the lowest, then BioC, BioDn, and CreX at progressively higher concentrations — allowing verification of sensitivity and signal proportionality. In the provided data the ascending trend is visible: BioB-3 ≈ 0.73, BioC-3 ≈ 1.30, BioDn-3 ≈ 7.28, CreX-3 ≈ 14.80.

The DapX, LysX, PheX, ThrX, TrpnX probesets have very low values (most below 0.1), expected as negative controls.

The `-3_at`, `-5_at`, and `-M_at` suffixes refer to probes targeting the **3' end, 5' end, and middle** of each transcript. The 3'/5' ratio is a RNA degradation QC metric — degraded RNA produces relatively less 5' signal.

**Ribosomal RNA controls (18srRna, 25srRna)**

Target yeast 18S and 25S ribosomal RNA sequences. Serve as hybridization and signal intensity checks.

**Yeast gene controls (AFFX-YEL..., AFFX-YER..., AFFX-YFL...)**

Target specific *S. cerevisiae* genes. On chips designed for non-yeast organisms they function as cross-hybridization checks; on yeast chips some are genuine housekeeping genes.

**Are they used in normalization?**

Generally **no** — not in the GeneSpring V7 per-chip median normalization. Their role is QC *before and after* normalization, not normalization itself. Standard practice was to inspect them to decide whether a chip was worth including in analysis at all.

---

### User
*(Provided excerpt from previous response about per-chip normalization preceding steps)*

The microarray data from this paper is stored at https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE3431. Were the preprocessing steps like background subtraction and ratio computation already performed resulting in this data, or is that something I would need to do myself? What about other data processing steps?

### Assistant

**What the GEO data for GSE3431 almost certainly represents**

This is the Affymetrix YG-S98 yeast genome array — a single-color chip, not a two-color cDNA array. The earlier mention of Cy5/Cy3 ratios and background subtraction applies to two-color spotted arrays, not this dataset.

The values in the data file are characteristic of **MAS5-processed signal intensities that have already been subjected to per-chip scaling** — not raw probe intensities (which would be large integers in the hundreds to thousands) and not log-transformed values (which would be negative for low expressors).

**What has already been performed:**

1. **Background correction** — MAS5 uses PM and MM probe pairs to estimate and subtract non-specific signal at the probe level.
2. **Probe summarization** — the ~11–20 individual probe pairs per probeset have been collapsed into a single signal value per gene.
3. **Per-chip scaling** — MAS5 applies a linear scaling to the summarized gene-level intensities to bring chips to a common target intensity.

**What still needs to be done to replicate the paper's analysis:**

The GeneSpring per-chip median normalization would be an *additional* step applied on top of the MAS5 values — dividing each value in each chip's file by the median of all values on that chip, bringing every chip's median to 1.0. Per-gene normalization (if used) would come after that.

**Summary:** You do not need to perform background subtraction, ratio computation, or probe summarization yourself. What remains is the cross-chip normalization in GeneSpring (or its equivalent in R/Python).

---

### User
*(Provided excerpt from previous response about per-gene normalization)*

There is no clear reference set of samples - the only data I am aware of are the microarray samples that were directly used in the analysis. Can you tell if this step you mentioned (per gene normalization) was actually used in the paper?

### Assistant

I was unable to access the full supplemental methods text through search. However, based on the dataset characteristics:

The values in the GEO files are in raw MAS5 signal units (ranging roughly 0.005 to 44), inconsistent with having gone through per-gene normalization. If per-gene normalization had been applied, every gene's median across all 36 timepoints would be exactly 1.0, with values distributed tightly around 1.

For this specific paper, it makes good scientific sense to skip per-gene normalization. The central question is about temporal periodicity — for that kind of analysis you want to preserve meaningful cross-timepoint variation.

**Practical implication:** The most defensible starting point is to apply the per-chip median normalization to the GEO data and stop there — matching what's explicitly stated in the supplemental. Per-gene normalization may have been applied within GeneSpring for visualization purposes (heatmaps), but probably wasn't part of the core identification of periodic genes.

---

### User
*(Provided the full supplemental PDF: `tu_som__2_.pdf`)*

Here's the supplemental information, does this change anything?

### Assistant

The supplemental confirms the pipeline. The key sentence from page 3:

> "The raw expression data was normalized per chip using Genespring v7 and then used for periodicity and cluster analysis."

And from the clustering analysis section:

> "To facilitate the comparison of genes with different average expression levels, the temporal profiles of periodically expressed genes were normalized to a common expression level and dispersion."

**Clarification:** The per-gene normalization step was **not** a GeneSpring per-gene-to-median operation. Instead, a custom normalization was applied specifically for the clustering step only — applied to already-identified periodic genes, not all genes globally. They averaged profiles over three periods and normalized to a common expression level and dispersion, which sounds more like a z-score standardization (subtract mean, divide by standard deviation) per gene to make periodic profiles visually comparable in cluster plots.

**Final pipeline:**
1. MAS5 processing (already done in the GEO data)
2. Per-chip median normalization in GeneSpring (divide each chip by its median → what you'd need to do)
3. Periodicity analysis and clustering on that result
4. A separate z-score-like normalization applied *only* to periodic genes *only* for visualization in cluster figures

No per-gene-to-median step is involved at all in the main analytical pipeline.

---

### User
*(Provided excerpt from previous response about MAS5 signal characteristics)*

Does this still appear to be correct based on the supplemental information? And, are these raw intensities or log-scaled intensities?

### Assistant

Yes, the characterization still holds up. The methods confirm they used Affymetrix YG_S98 arrays processed through GeneChip Operating Software (GCOS) v1.1, which outputs MAS5-style signal intensities, before importing into GeneSpring.

**On log scaling — the values are not log-transformed.** Several things point to this:

The supplemental figure axes (Figs S1 and S2) show a scale running 0.1 → 1 → 10, which is a log scale on the *display*, but the underlying stored data values range from below 1 to above 10, matching the GEO files exactly. If the stored values were already log-transformed, you'd expect them centered around 0 with both positive and negative values.

MAS5 outputs are by convention stored and distributed on a linear scale. The per-chip normalization in GeneSpring divides by the chip median — an operation that makes sense on linear values and would be nonsensical on log-transformed ones.

Values like 0.079 and 0.005 are consistent with linear MAS5 output for absent/near-background probesets after scaling — not puzzling outliers.

**Summary:** MAS5-processed, linear scale, consistent with what the supplemental describes.

---

### User
Intensity is roughly proportional to concentration, correct? Also, how were these P (present) calls generated?

> "We classified those probes that had at least 3 (on average one per cycle) 'present' (P) calls (as generated by Affymetrix GeneChip software) as expressed. According to this criterion, out of 9,335 probes queried by the YG_S98 array, 7,985 (~86%) were expressed. Out of 6,555 probes querying unique, annotated ORFs, 6,209 were expressed." - Supplemental Information

### Assistant

**On intensity and concentration**

Roughly yes, but with important caveats. Within a reasonable dynamic range, hybridization signal is proportional to the abundance of the target mRNA. However the relationship isn't perfectly linear across the full range — at very low concentrations signal approaches background noise and becomes unreliable, and at very high concentrations probes can saturate. MAS5 processing helps with the low end by using mismatch probes to model background, but saturation at the high end is a real limitation.

**On the Present/Marginal/Absent calls**

These are generated by MAS5 as part of its core algorithm. Each probeset contains multiple probe pairs — typically 11–20 pairs for the YG_S98. Each pair consists of a perfect match (PM) probe and a mismatch (MM) probe identical except for a single base change at the center position.

For each probe pair, MAS5 computes a discrimination score based on the PM/MM relationship. If a transcript is genuinely present, the PM should be consistently and substantially brighter than the MM across most probe pairs. If the gene is absent, PM and MM intensities will be similar (both reflecting only non-specific background).

MAS5 runs a one-sided Wilcoxon signed-rank test across all probe pairs in a probeset, asking whether PM intensities are significantly greater than MM intensities. The resulting p-value is converted to a call:
- **Present** (p < 0.04)
- **Marginal** (0.04 ≤ p ≤ 0.06)
- **Absent** (p > 0.06)

Tu et al. required at least 3 Present calls across the 36 timepoints — rather than just one — because a single P call could be noise or a transient artifact, while 3 calls (roughly one per cycle) provides more confidence that the gene is genuinely expressed at some point during the experiment.

---

### User
Are all genes in the GEO accession page for this dataset present? If not, how do I set them apart?

### Assistant

The GEO data will contain all 9,335 probesets on the YG_S98 array — GEO deposits the full array output, not a pre-filtered subset. Absent/low-confidence probesets are included right alongside the expressed ones.

To identify which probesets pass the "at least 3 Present calls" filter, you need the detection call data, which is separate from the signal intensity data. In Affymetrix GEO submissions there are typically two value columns per sample: the signal intensity and the detection call (P, M, or A). You'd need to check whether the GSE3431 sample records include the detection call column — some submitters include it, some only deposit signal values.

To check this practically, look at an individual sample's SOFT file. If calls are included you'll see a column labeled something like `Detection` or `ABS_CALL` alongside the `VALUE` column. If present, the filter would be: for each probeset, count timepoints where the call is "P", and retain only those with ≥ 3.

**If detection calls were not deposited**, options include:
1. Re-derive them from raw CEL files (if deposited) using `mas5calls()` in R's `affy` package, which reimplements the Wilcoxon-based algorithm.
2. Apply a simple intensity threshold as a proxy (less principled).
3. Use all 7,985 expressed probesets the authors report, acknowledging you cannot perfectly reconstruct their exact filter without the call data.
