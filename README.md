# SEA-AD Microglia snRNA-seq Analysis

## Summary

Microglia are the brain's resident immune cells. In Alzheimer's disease (AD) they shift out of their normal surveillance state into an activated one. Keren-Shaul et al. (2017) described this in mice and called the activated cells **disease-associated microglia (DAM)**. They showed the change happens in two steps: a first step that does not need the TREM2 gene, and a second that does. That signature was defined in **mouse** cells. This project explores whether it holds up in **human** brain tissue.

The data comes from the Seattle Alzheimer's Disease Brain Cell Atlas (SEA-AD), which profiled single nuclei from 84 donors spanning the full range of AD pathology (no disease to severe). Working from this, I scored each microglial nucleus against the mouse DAM and homeostatic gene lists, averaged those scores per donor, and tested them against the donors' neuropathology and cognitive records. 

What I found out was that the mouse signature transfers only partially**.**

### Gene Signatures

Using the differential expression results from Keren-Shaul et al. (2017) Supplementary Table S3 ("Differential Expression Analysis of Homeostatic Microglia to DAM"), with stage labels taken from their Figure 2\.

A gene was kept only if it had a clear one-to-one mouse–human match, was statistically significant in the source (FDR \< 0.05), and was detected in at least 5% of nuclei.

**DAM signature (27 genes)**

*Stage 1 (no TREM2):* `APOE` `TYROBP` `B2M` `FTH1` `CTSB` `CTSD` `CSTB` `TIMP2`

*Stage 2 (needs TREM2):* `CST7` `CLEC7A` `LPL` `ANKH` `SPP1` `AXL` `CSF1` `ITGAX` `CADM1` `CD63` `CD9` `SERPINE2` `GUSB` `CTSZ` `HIF1A` `CTSL` `CD68` `CTSA` `TREM2`

**Homeostatic signature (26 genes)** 

`TMEM119` `P2RY12` `P2RY13` `TXNIP` `CX3CR1` `SERINC3` `TGFBR1` `GLUL` `MAF` `CD164` `ZFHX3` `SRGAP2` `RHOB` `CMTM6` `ADGRG1` `LPCAT2` `MARCKS` `SLCO2B1` `SELPLG` `SALL1` `BIN1` `ITGAM` `PMEPA1` `OLFML3` `EPB41L2` `PTGS1`

**Negative control (7 genes):** microglial genes that barely change between the two states in the source data. If these move with disease, the signal is likely technical rather than biological.

`HEXB` `C1QA` `C1QB` `C1QC` `CTSS` `CSF1R` `CST3`

**Excluded:** `H2-D1`, `Lyz2`, `Ccl6` (no clean human equivalent) and `CD52` (found in only 0.3% of nuclei).

---

## How to Set Up

### 1\. Clone the repository

git clone https://github.com/wnguyen36/SEAAD-microglia-analysis.git

cd SEAAD-microglia-analysis

### 2\. Create the environment

Built and tested on Python 3.10.2.

conda create \-n seaad python=3.10.2

conda activate seaad

pip install \-r requirements.txt

### 3\. Download the data

Download them from the SEA-AD portal and place them in `data/`.

Main portal: [https://brain-map.org/consortia/sea-ad/our-data](https://brain-map.org/consortia/sea-ad/our-data)

| File | Save as | Notes |
| :---- | :---- | :---- |
| Microglia-PVM snRNA-seq object | `data/SEAAD_microglia.h5ad` | \~2 GB. Under "Single nucleus omics" |
| Donor clinical metadata | `data/SEAAD_donor_metadata.xlsx` | Age, sex, Braak, CERAD, APOE, cognitive status |
| Quantitative neuropathology | `data/SEAAD_neuropathology.csv` | Iba1, AT8, 6e10, NeuN image measurements |

**Version note:** SEA-AD updated several files in June 2026\. This analysis used the versions available before that update. The neuropathology measurements used here are from the middle temporal gyrus (MTG) only.

### 4\. Run the notebook

jupyter notebook notebooks/SeaAD\_Analysis.ipynb

Run cells in order from the top. Figures save to `figures/`, tables to `results/`.

### Repository layout

SEAAD-microglia-analysis/

├── data/                       \# downloaded SEA-AD files (not tracked)

├── figures/                    \# generated plots of note

├── notebooks/

│   └── SeaAD\_Analysis.ipynb    \# the full analysis

├── results/                    \# all generated plots (not tracked)

├── LICENSE

└── README.md

---

## Pipeline

*(to be written)*

---

## Figures

### 1\. Microglial subtypes and activation scores

Supertypes and scores on UMAP

SEA-AD's microglial subtypes shown side by side with the DAM and homeostatic scores.

### 2\. Activation profile of each subtype

Activation profiles by supertype

Average DAM and homeostatic score for every subtype, calculated per donor first. `Micro-PVM_3-SEAAD` is the most DAM-like of the well-represented subtypes. **Micro-PVM\_4-SEAAD appears highest but has only 2 donors and is not interpretable.**

### 3\. Subtype abundance across Braak stages

Subtype abundance by Braak stage

How the proportions of the three main subtypes shift as tau pathology worsens. `Micro-PVM_3-SEAAD` (purple) grows while `Micro-PVM_2` (green) shrinks. Braak 0 and II have too few donors to be trusted and are marked in red.

### 4\. Composition and score against tissue staining

Composition vs histology

Compares two ways of measuring the same thing against protein staining from the same tissue. Counting cells of the DAM-like subtype tracks tau strongly (rho \= 0.51); averaging DAM scores tracks overall microglial abundance instead (rho \= 0.28). The right-hand column is the negative control.

### 5\. DAM score across Braak stages

DAM score by Braak stage

Average DAM score per donor, grouped by tau stage. The upward trend is real but modest, and the spread within each stage is wide.

### 6\. DAM score against pathology measures

DAM correlations

Correlation between each donor's average DAM score and their pathology and cognitive measures. Four of six are significant.

### 7\. APOE4 carriers versus non-carriers

APOE4 status

Four measures compared between APOE4 carriers and non-carriers. Only the negative control panel reaches significance, which is why this result is reported as null.

### 8\. DAM score by APOE genotype

DAM score by APOE genotype

The same data split by exact genotype. Scores drift upward with more copies of E4, but two genotype groups have fewer than 3 donors and cannot be interpreted.

### 9\. Genes differing between dementia and no-dementia donors

Volcano plot

Every gene tested, plotted by size of difference (left–right) against confidence (up–down). Genes higher in dementia sit on the left. Circled points are the pre-selected signature genes, marked so they are not mistaken for discoveries.

---

## Results

After removing non-microglial nuclei and restricting to the MTG, the analysis covers 35,907 nuclei from 84 donors. Since all statistics are calculated by donor, the sample size is 84\.

### 1\. Count cells instead of averaging

The clearest result is from determining which subtype of microglia a donor has predicts their pathology much better than the average activation score of their microglia.

| Measure | % of DAM-like subtype | Average DAM score |
| :---- | :---- | :---- |
| CERAD (plaques) | **0.52** | 0.26 |
| AT8 staining (tau) | **0.51** | 0.23 |
| Cognitive status | **0.48** | 0.29 |
| Braak stage (tau) | **0.45** | 0.27 |
| Thal phase (amyloid) | **0.45** | 0.26 |
| 6e10 staining (amyloid) | **0.34** | 0.21 |
| Iba1 (microglial amount) | 0.07 | **0.28** |

*(Spearman correlation, higher \= stronger relationship)*

This is simply due to how each subtype is weighted. The DAM-like subtype (Micro-PVM\_3-SEAAD) only makes up 14% of the microglia, so when it grows between healthy and dementia donors, the average score will not see a great increase. 

**The subtype expands with disease:**

- With tau stage (Braak III–VI): rho \= 0.40, adjusted p \= 0.002  
- With dementia: 9.3% → 15.3% of microglia, adjusted p \= 0.0001  
- With tau staining (AT8): rho \= 0.51, adjusted p \< 0.0001

### 2\. Two processes

Two measurements that looked similar turned out to capture separate processes.

Recalculating the DAM score using only ordinary `Micro-PVM_2` microglia:

|  | Iba1 (microglial amount) | AT8 (tau) |
| :---- | :---- | :---- |
| All microglia | 0.28 | 0.23 |
| `Micro-PVM_2` only | **0.28** | **0.12 (null)** |

This table concludes that ordinary microglia raise their DAM genes slightly in donors with more microglia overall whereas tau pathology is tied specifically to the DAM-like subtype becoming more common (not to ordinary microglia changing).

### 3\. Mouse signature only transfers partially

| Gene | Effect size in mouse | Detected in human nuclei |
| :---- | :---- | :---- |
| `CST7` | \+6.08 (strongest in the source) | **1.0%** |
| `CSF1` | \+5.23 | **0.5%** |
| `LPL` | \+4.95 | 5.5% |
| `SERPINE2` | \+2.00 | 2.9% |

Homeostatic markers show the same problem: `TMEM119` at 21%, `HEXB` at 24%, `SALL1` at 20%. 

Other reasons why the mouse framework does not map cleanly:

- The two DAM stages barely track each other (rho \= 0.33).   
- At the single-nucleus level the DAM and homeostatic scores do not oppose each other (rho \= \+0.05). 

### 4\. Finding human activation genes

Comparing donors with and without dementia across all 17,351 well-detected genes, without using any preset list, gave **319 significant genes**. The ones higher in dementia are recognisable human microglial activation markers:

`GPNMB` · `LGALS3` (galectin-3) · `CHI3L1` (YKL-40, a clinical AD biomarker) · `CD44` · `PLIN2` · `S100A4` · `LYZ` · `FLT1`

None of these are in the mouse DAM list. For studying human tissue, I believe these genes are worth looking at, instead of the mouse signatures.

Signature genes also moved in the expected directions: `CTSD` (adj. p \= 0.015), `CD9` (0.040) and `LPL` (0.045) higher in dementia; `CX3CR1`, `P2RY12` and `P2RY13` higher in donors without dementia, though not significantly.

### 5\. Negative controls and what they mean

A set of microglial genes that barely change in the source data was scored alongside everything else. Implementing this ruled out some statements:

Sequencing depth is not driving the results. Per donor, DAM score versus depth was −0.004.

Tau and cognition are subtype-specific; amyloid is not. Comparing each result against the control set:

| Measure | Specificity gap |
| :---- | :---- |
| Cognitive status | **0.41** |
| Braak (tau) | **0.34** |
| AT8 (tau) | **0.32** |
| CERAD | 0.27 |
| Thal (amyloid) | 0.15 |
| 6e10 (amyloid) | 0.14 |

Amyloid measures move the control genes almost as much as the DAM genes.

### 6\. Null results

- **APOE4 carriers.** No measure survived correction. The negative control was in fact the *most* significant panel (p \= 0.045). Removing `APOE` from the score barely changed it (rho \= 0.98), so the result was not circular either.  
- **Signature enrichment in the differential expression.** DAM genes did rank toward the dementia side (27.6th percentile), but the negative control ranked the same way (25.0th). Inconclusive.  
- **Age at death, & overall AD neuropathological change.** Neither correlated with the DAM score.

---

## Limitations

**Nuclei** lack most cytoplasmic RNA. The mouse signature was defined in whole cells, so there may have been some poor transfer.

**Microglia and perivascular macrophages (PVM) are indistinguishable.** SEA-AD labels them together as "Microglia-PVM," with no subtype separating them. Perivascular macrophages express several DAM genes, so a fraction of the signal may come from them.

**Only one brain region.** MTG was chosen because it was the only region present in all 84 donors and because the neuropathology measurements come from it. This may not hold elsewhere in the brain.

**Potential negative control set flaws.** It includes complement genes (`C1QA/B/C`), which rise in human AD. Removing them leaves only four genes, too few to be reliable. A better approach would compare against many randomly chosen gene sets matched for expression level.

**Sample size distribution.** 84 donors, however, are spread unevenly across stages. Braak 0 has 2 donors and Braak II has 4\. Those stages were excluded from trend tests.

**Proportions are linked.** Subtype percentages must add to 100%, so one going up forces another down. Results are also reported as a ratio between two subtypes, which avoids this, but one subtype going up does not necessarily mean the other is inhibited.

**Residual contamination.** `BANK1` and `PRKCQ` (B- and T-cell genes) appear among genes higher in no-dementia donors, and 16% of the significant novel genes are unnamed lncRNAs. Both suggest leftover ambient RNA.

## Citations

**Data**

Gabitto, M. I., Travaglini, K. J., et al. (2024). Integrated multimodal cell atlas of Alzheimer's disease. *Nature Neuroscience*, 27, 2366–2383. doi:10.1038/s41593-024-01774-5

Seattle Alzheimer's Disease Brain Cell Atlas (SEA-AD), Allen Institute for Brain Science. [https://portal.brain-map.org/explore/seattle-alzheimers-disease](https://portal.brain-map.org/explore/seattle-alzheimers-disease)

**Gene signatures**

Keren-Shaul, H., Spinrad, A., Weiner, A., et al. (2017). A unique microglia type associated with restricting development of Alzheimer's disease. *Cell*, 169(7), 1276–1290. doi:10.1016/j.cell.2017.05.018