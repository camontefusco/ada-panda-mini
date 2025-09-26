# `ada-panda-mini`

*A minimal, reproducible simulation of ADA drug interference and PandA correction.*

👉 For background on **why ADA assays matter, case studies, pharma workflows, and the role of data science**, see [introduction.md](./introduction.md).

![Banner](banner.png)

## 🚀 Executive Summary

- **Problem:** Standard ADA assays underestimate immunogenicity at therapeutic drug levels due to drug interference.  
- **Solution:** This repo simulates **PandA (PEG + Acid) correction**, showing how it restores ADA detection, improves drug tolerance, and reduces assay bias.  
- **Impact:** Demonstrates how **bioanalytical method choice** affects PK, exposure–response, and dose justification, with outputs formatted for **regulatory reports** and **digital health standards**.

---

## 🔬 Purpose

Therapeutic drug in serum can **mask anti-drug antibodies (ADA)** in bridging immunoassays, causing **false negatives** and **underestimation of immunogenicity**.  
The **PandA method** (*PEG precipitation + Acid dissociation*) was developed to overcome this interference and achieve **high drug tolerance** in ADA detection.

This repo provides a **minimal, reproducible simulation** of:

- How **drug interference** biases ADA recovery in a standard assay  
- How **PandA-style correction** restores ADA detection  
- How this difference propagates to **PK exposure, target attainment, and dose recommendations**  
- How results align with **literature benchmarks** and **regulatory expectations**  

---

## 📚 Literature anchors

- **Zoghbi et al., 2015**: Original PandA paper; shows PEG + acid dissociation achieves high drug tolerance. PMID: [26255760](https://pubmed.ncbi.nlm.nih.gov/26255760/)  
- **FDA Immunogenicity Assay Guidance (2019)**: Assay validation, drug tolerance, and tiered testing strategy. [PDF](https://www.fda.gov/media/119788/download)  
- **Celerion Poster (2020)**: PandA detected 10 ng/mL ADA at **800 µg/mL drug** — dramatic improvement vs bridging.  
- **Sanofi EBF 2024**: Practical defaults: **6% PEG precip**, **3% PEG wash**, **acetic acid 300 mM (pH 2.5–3.5, ~30–45 min)**; caution that ≥60 min acid can reduce ADA signal. [PDF](https://e-b-f.eu/wp-content/uploads/2024/01/D1-44.-Brendy-Van-Butsel-Ortwin-Vande-Vyver-sanofi.pdf)  

---

## 🧪 Method overview

**PandA critical steps:**
1. **Complex formation**: Saturate ADA with drug to capture all ADA.  
2. **PEG precipitation**: 6% PEG (cold), pellet complexes, wash ×2 with 3% PEG.  
3. **Acid dissociation & coating**: Acetic acid 300–500 mM (pH 2.5–3.5), 30–45 min, coat directly on high-bind plates under acidic conditions.  
4. **Detection**: Specific anti-Ig detection (e.g. ECL), blockers & buffers tuned to reduce background.

**Trade-offs:**  
- Too harsh acid or long incubation (≥60 min) can damage ADA → reduce dynamic range.  
- Method chosen based on **required drug tolerance**: bridging OK up to low µg/mL; PandA for ≥100s µg/mL to mg/mL.  

---

## 🖼️ PandA Workflow (schematic)

![PandA workflow](./docs/panda_workflow.png)

*PandA improves ADA drug tolerance by precipitating complexes (PEG), 
acid-dissociating them under controlled pH, and detecting freed ADA with 
specific anti-Ig reagents. Default parameters shown are industry-reported 
(Sanofi, 2024).*

---

## 📊 What this repo simulates

- **01_simulate**: Generate synthetic PK + true ADA titers.  
- **02_measure**: Apply drug-dependent masking (standard assay bias).  
- **03_panda**: Apply PandA-style correction; compare vs true ADA.  
- **04_benchmark_vs_literature**: Overlay simulated recovery vs literature thresholds; flag **PASS/ALERT**.  

**Outputs (`/reports`):**
- `tlgs.parquet`:  
  - `drug_tolerance` (drug conc vs recovery)  
  - `ada_bias` (measured vs true vs corrected)  
  - `target_attainment` (% above threshold exposure)  
- `benchmarks.parquet`: PASS/ALERT flags  
- Figures:  
  - Drug tolerance curve  
  - ADA time series (True vs Measured vs PandA)  
  - Bias vs drug concentration  
  - Target attainment impact  
  - PASS/ALERT heatmap  

---

## 🛠️ Method parameters (defaults)

These reflect Sanofi’s EBF 2024 “default PandA,” safe for most programs:

```yaml
PEG_precip: 6% (cold)
PEG_wash: 3% (×2)
Acid: Acetic acid 300 mM, pH 2.8–3.2
Acid_time: 30–45 min
```
## Tuning guide (beginner-friendly):
- Need higher tolerance (>mg/mL drug): consider Glycine-HCl (10–50 mM, pH 2.5–3.0).
- Low recovery: adjust PEG % (5–7%), shorten acid incubation, test alternative acids.
- Signal loss/dynamic range shrinkage: avoid acid ≥60 min, reduce acid strength, check for ADA denaturation.
- High background: optimize molar excess of labeled reagents, blockers, wash rigor.

## 🧾 Validation metrics (aligned to FDA 2019)
- Sensitivity: lowest ADA concentration reliably detected (LLOQ).
- Drug tolerance: max drug concentration where recovery ≥80%.
- Target interference: confirm assay still detects ADA in presence of target antigen.
- Specificity: confirmatory inhibition with drug.
- Precision: intra-/inter-assay CVs for NC/LPC/HPC.
- Cut-points: Screening (SCP) & Confirmatory (CCP) derived from NCs.

## ▶️ How to run
```bash
git clone https://github.com/YOURNAME/ada-panda-mini
cd ada-panda-mini
mamba env create -f env.yml
mamba activate ada-panda
make all   # runs 01→04, saves figures + tlgs + benchmarks
```
Outputs will appear in /reports.

---

## 📈 Results

### 1. Drug-tolerance curves
![Drug tolerance curve](reports/figures/01_drug_tolerance_curve.png)

**Interpretation:**  
- The **standard bridging assay** shows a sharp drop in ADA recovery above ~10 µg/mL drug, consistent with drug masking and loss of sensitivity.  
- The **PandA-corrected assay** maintains recovery ≥80% up to ~100–500 µg/mL drug, in line with literature reports (Zoghbi 2015; Sanofi EBF 2024).  
- **Regulatory meaning:** drug tolerance must be validated; bridging alone would fail to meet tolerance requirements for therapeutic exposures.

---

### 2. ADA time series (True vs Measured vs PandA)
![ADA time series](reports/figures/02_ada_time_series.png)

**Interpretation:**  
- **True ADA** rises steadily after dosing.  
- **Measured (uncorrected)** underestimates ADA during periods of high drug concentration, masking immunogenicity.  
- **PandA-corrected** closely tracks the true ADA profile.  
- **Regulatory meaning:** Without correction, trial reports would understate ADA incidence, misinforming risk assessment.

---

### 3. Measurement bias vs drug concentration
![Bias by drug bin](reports/figures/03_bias_by_drug_bin.png)

**Interpretation:**  
- **Bias** in standard assay grows increasingly negative as drug increases, reflecting systematic under-recovery.  
- **PandA correction** yields near-zero bias across drug bins.  
- **Regulatory meaning:** FDA guidance requires demonstration of bias control; PandA provides evidence of acceptable accuracy.

---

### 4. Impact on PK target attainment
![Target attainment impact](reports/figures/04_target_attainment.png)

**Interpretation:**  
- **Uncorrected assay** overestimates the proportion of patients achieving exposure targets.  
- **PandA-corrected data** reveals fewer patients above threshold, especially ADA-positive subjects.  
- **Regulatory meaning:** Bioanalytical method choice directly influences dose recommendations and labeling.

---

### 5. PASS/ALERT heatmap
![PASS ALERT heatmap](reports/figures/05_pass_alert_heatmap.png)

**Interpretation:**  
- **Standard assay** triggers ALERT at drug levels ≥10 µg/mL, failing the ≥80% recovery benchmark.  
- **PandA** yields PASS up to high µg/mL–mg/mL ranges.  
- **Regulatory meaning:** clear visual justification for method selection in validation reports.

---

## 🧾 Summary

- **Assay artifact:** Standard bridging ADA assays underestimate ADA at therapeutic drug levels.  
- **Solution:** PandA improves drug tolerance, maintains recovery, and reduces bias.  
- **Downstream impact:** Corrected ADA profiles reveal true clearance increases and lower target attainment in ADA-positive patients.  
- **Clinical pharmacology relevance:** Bioanalytical method choice feeds directly into PK/PD, exposure–response, and ultimately **dose selection**.  
- **Regulatory alignment:** This simulation reflects expectations from FDA/EMA to validate drug tolerance and document method selection.

Plate: High-bind, acidic during coat
Detection: Anti-Ig, ECL, blockers optimized
