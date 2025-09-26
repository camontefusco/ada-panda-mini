# ada-panda-mini
*A minimal, reproducible simulation of ADA drug interference and PandA correction.*

👉 For background on why ADA assays matter, case studies, pharma workflows, and the role of data science, see [introduction.md](introduction.md).

![Banner](banner.png)

---

## 🚀 Executive Summary
**Problem**  
Standard ADA assays underestimate immunogenicity at therapeutic drug levels due to drug interference.  

**Solution**  
This repo simulates PandA (PEG + Acid) correction, showing how it restores ADA detection, improves drug tolerance, and reduces assay bias.  

**Impact**  
Demonstrates how bioanalytical method choice affects PK, exposure–response, and dose justification, with outputs formatted for regulatory reports and digital health standards.  

---

## 🔬 Purpose
- **Drug interference**: Therapeutic drug in serum can mask anti-drug antibodies (ADA) in bridging immunoassays, causing false negatives.  
- **PandA method**: PEG precipitation + Acid dissociation overcomes this interference and achieves high drug tolerance in ADA detection.  

This repo provides a **minimal reproducible simulation** of:
1. How drug interference biases ADA recovery in a standard assay  
2. How PandA-style correction restores ADA detection  
3. How this difference propagates to PK exposure, target attainment, and dose recommendations  
4. How results align with literature benchmarks and regulatory expectations  

---

## 📚 Literature Anchors
- **Zoghbi et al., 2015** – Original PandA paper, PEG + acid achieves high drug tolerance (PMID: 26255760)  
- **FDA Immunogenicity Assay Guidance (2019)** – Assay validation, drug tolerance, tiered testing  
- **Celerion Poster (2020)** – PandA detects 10 ng/mL ADA at 800 µg/mL drug  
- **Sanofi EBF 2024** – Practical defaults: 6% PEG precip, 3% PEG wash, 300 mM acetic acid, 30–45 min  

---

## 🧪 Method Overview
### PandA critical steps
1. **Complex formation** – Drug saturates ADA  
2. **PEG precipitation** – Pellet complexes (6% PEG)  
3. **Acid dissociation** – Acetic acid 300–500 mM, pH 2.5–3.5, 30–45 min  
4. **Detection** – ECL anti-Ig detection, optimized blockers  

**Trade-offs**  
- Too strong acid or ≥60 min → ADA denaturation (signal loss)  
- PandA chosen for drug tolerance ≥100 µg/mL; bridging alone suffices only for low µg/mL  

---

## 📊 What this repo simulates
### Notebooks
- **01_simulate** – Generate synthetic PK profiles, ADA status, assay recovery curves  
- **02_measure** – Apply drug-dependent masking (standard vs PandA assay detection)  
- **03_exposure_response** – Show downstream impact on PK troughs, ADA detection, and exposure–response  
- **04_benchmark_vs_literature** – Compare simulated recovery vs published benchmarks; flag PASS/ALERT  

### Outputs (in `/reports`)
- **Parquet files**  
  - `tlgs.parquet`: tolerance + bias metrics  
  - `benchmarks.parquet`: PASS/ALERT flags  
- **Figures**  
  - Drug tolerance curve  
  - ADA time series (true vs measured vs PandA)  
  - Detection bias vs drug concentration  
  - Exposure–response plots  
  - PASS/ALERT table  

---

## 🛠️ Method Parameters (defaults)
- PEG_precip: 6% (cold)  
- PEG_wash: 3% (×2)  
- Acid: Acetic acid 300 mM, pH 2.8–3.2  
- Acid_time: 30–45 min  

Tuning guide included in code comments.  

---

## 🧾 Validation Metrics (aligned to FDA 2019)
- Sensitivity (LLOQ ADA)  
- Drug tolerance (≥80% recovery threshold)  
- Target interference  
- Specificity (drug inhibition confirmatory)  
- Precision (intra-/inter-assay CVs)  
- Cut-points (SCP, CCP)  

---

## ▶️ How to run
```bash
git clone https://github.com/YOURNAME/ada-panda-mini
cd ada-panda-mini
mamba env create -f env.yml
mamba activate ada-panda
make all   # runs notebooks 01 → 04, saves figures + parquet outputs
```
Outputs will appear in /reports.

## 📈 Results

- **Drug tolerance curves** – Standard fails above ~10 µg/mL; PandA passes up to ~500–1000 µg/mL  
- **ADA time series** – PandA restores ADA detection masked by standard assays  
- **Bias vs drug concentration** – Standard shows strong negative bias; PandA corrects  
- **Exposure–response** – PandA shows true clearance effects, ADA+ patients with lower troughs  
- **PASS/ALERT benchmarks** – PandA passes FDA-style drug tolerance; standard triggers ALERT  

---

## 🧾 Summary

- **Assay artifact**: Standard bridging assays underestimate ADA at therapeutic levels  
- **Solution**: PandA improves drug tolerance and reduces bias  
- **Downstream impact**: Corrected ADA detection changes PK/PD and dose justification  
- **Regulatory alignment**: Reflects FDA/EMA expectations to document drug tolerance validation  

---
## 📂 Repo structure
```bash
ada-panda-mini/
├── notebooks/
│   ├── 01_simulate.ipynb
│   ├── 02_measure.ipynb
│   ├── 03_exposure_response.ipynb
│   ├── 04_benchmark_vs_literature.ipynb
├── reports/
│   ├── figures/
│   ├── tlgs.parquet
│   ├── benchmarks.parquet
├── env.yml
├── introduction.md
├── README.md
```
---

## 📬 Contact
Carlos Montefusco
📧 cmontefusco@gmail.com
🔗 GitHub: /camontefusco
