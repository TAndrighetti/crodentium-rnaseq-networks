# Systems biology analysis of *Citrobacter rodentium*
Tahila Andrighetti  
September 2025

## Overview

This repository contains the **computational analysis workflows** used to investigate how a diet-derived compound (**Compound X**) alters the **growth, transcriptional programs, metabolism, and virulence-associated networks** of *Citrobacter rodentium*.

> **Note on terminology**  
> The compound referred to as **Compound X** is a **placeholder name**.  
> This designation is used intentionally because the data associated with this project have **not yet been published**, allowing the analytical workflows and code structure to be shared without disclosing unpublished experimental details.

---

## Experimental context

- *Citrobacter rodentium* was cultured *in vitro* in media supplemented with:
  - fecal filtrate from mice fed a **Compound X–enriched diet**
  - fecal filtrate from control mice
- Bacterial growth was **increased** in the presence of fecal filtrate derived from **Compound X–fed mice**.
- RNA-seq and downstream analyses were performed to understand how this altered environment impacts **bacterial metabolism and virulence-related pathways**.

---

## Analytical goals

- Identify **differentially expressed genes** in *C. rodentium* associated with exposure to Compound X–modified fecal environments.
- Prioritize DEGs linked to **virulence factors** and **carbon metabolism** (with emphasis on fructose- and malate-related pathways).
- Contextualize transcriptional changes within **high-confidence protein–protein interaction networks** to identify key connectors, modules, and potential regulatory bottlenecks.

---

## Repository scope and structure

This repository is designed to showcase **analysis pipelines, code organization, and reproducible computational workflows**.  
It focuses on **methods and implementation**, rather than biological interpretation of unpublished results.

- Data files are not distributed.
- Notebooks are structured as **analysis workflows**, not step-by-step tutorials.
- Parameters and decisions reflect an **iterative, real-world single-project analysis process**.

### Repository structure

```bash
.
├── env/
│   └── bacterial_systems_env.yml
│
├── figures_networks_results/
│   ├── net1.png
│   └── net2.png
│
├── notebooks_codes/
│   ├── 01.Virulence_Factors_DEGs.ipynb
│   ├── 02.Metabolites_related_DEGs.ipynb
│   └── 03.Networks.ipynb
│
├── PROJECT_NODES.md
└── README.md
```

---

## Main analysis workflow

- **01. Virulence factors (VF) analysis**
  - BLAST-based identification of virulence-associated DEGs using VFDB
- **02. Metabolite-related DEGs**
  - KEGG pathway–driven selection of genes linked to fructose and malate metabolism
- **03. Network analysis**
  - Integration of VF and metabolite-associated genes into STRING-derived interaction networks

Detailed methodological notes and analytical rationale are available in:

📄 `PROJECT_NOTES.md`


--- 

## RESULTS: Network visualizations

The resulting interaction networks generated in this analysis are included in this repository as two separate files: `net1.png` and `net2.png`.

Both files represent the same underlying high-confidence protein–protein interaction network, built from STRING data and filtered according to the criteria described in this project.  
The difference between them lies only in the visualization layout and styling choices applied during network rendering (e.g. node positioning, spacing, and visual emphasis).

