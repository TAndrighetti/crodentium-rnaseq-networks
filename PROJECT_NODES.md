# Project notes — Compound X effects on *Citrobacter rodentium*

## Overview

This document consolidates the **analytical reasoning, intermediate decisions, and technical notes** behind the computational workflows implemented in this project.  
While the `README.md` presents a concise overview, this file serves as a **living technical notebook** documenting how analyses were structured, refined, and interpreted during development.

> **Note on terminology**  
> The dietary compound investigated here is referred to as **Compound X**.  
> This placeholder name is used intentionally because the associated experimental data have **not yet been published**.

---

## 1. Biological motivation

- *Citrobacter rodentium* shows **increased in vitro growth** when cultured in fecal filtrates from mice fed a **Compound X–enriched diet**.
- This suggests that Compound X reshapes the intestinal environment in a way that:
  - alters bacterial metabolism,
  - impacts transcriptional programs,
  - and potentially modulates **virulence-associated pathways**.

> **Central question:**  
> How does exposure to a Compound X–altered fecal environment affect *C. rodentium* gene expression, virulence factors, and metabolic adaptation?

---

## 2. Data layers used

- **RNA-seq**
  - Differentially expressed genes (DEGs)
  - Protein sequences derived from CDSs (mapped to WP IDs)
- **Virulence factor references**
  - VFDB (Set B) protein sequences
- **Metabolic pathway references**
  - KEGG Pathway Maps (prefix `ROD_`)
- **Interaction networks**
  - STRING v12 physical protein–protein interactions for *C. rodentium*

---

## 3. Virulence factor–focused analysis

### 3.1 Strategy

- Identify which DEGs correspond to **known virulence factors (VFs)**.
- Prioritize VFs potentially linked to metabolic adaptation and growth advantage.

### 3.2 Implementation

(Full details implemented in `01.Virulence_Factors_DEGs.ipynb`.)

- Downloaded VF protein sequences from Virulence Factor Database **VFDB (Set B)**.
    > - **VFDB (Virulence Factor DataBase)** is a manually curated database that provides protein sequences, functional annotations, and mechanistic classification of bacterial virulence factors, widely used for genome annotation, comparative pathogenomics, and BLAST-based identification of virulence-associated genes 
    > - **Reference:** [Zhou SY, Liu B, Zheng DD, Chen LH and Yang J, 2025. VFDB 2025: an integrated resource for exploring anti-virulence compounds. Nucleic Acids Res. 53(D1):D871-D877.](https://academic.oup.com/nar/article/53/D1/D871/7848836)
    > - **Database URL:** [https://www.mgc.ac.cn/VFs/download.htm]

- Built a local BLASTp database. 
- Queried DEG-derived protein sequences against VFDB using BLASTp.
- Applied strict filtering in the BLASTp results:
  - `pident ≥ 50`
  - `evalue ≤ 1e-5`
- For proteins matching multiple VFs, retained a single best hit per WP ID using:
  1. lowest e-value
  2. highest percent identity
  3. highest bitscore

### 3.3 Output

- VF-only DEG tables (up, down, all).
- Heatmaps displaying the most strongly regulated virulence-associated genes.

---

## 4. Metabolite-related DEG analysis

(Implemented in `02.metabolites_related_DEGs.ipynb`.)

### 4.1 Pathway selection (KEGG)

Considering that the metabolites of interest are **Frucrose** and **Malate**, the selected KEGG pathways:

**Fructose-related pathways**
- ROD00051 — Fructose and mannose metabolism
- ROD00010 — Glycolysis / Gluconeogenesis
- ROD00520 — Amino sugar and nucleotide sugar metabolism

**Malate-related pathways**
- ROD00630 — Glyoxylate and dicarboxylate metabolism
- ROD00620 — Pyruvate metabolism
- ROD00020 — TCA cycle (no DEGs detected)

### 4.2 Protein-centric mapping approach

- Downloaded protein sequences for each pathway via **KEGG REST API**.
- Built local BLAST databases for each pathway.
- Queried the full *C. rodentium* proteome against pathway proteins.
- Selected best hits per pathway gene.
- Intersected BLAST hits with RNA-seq DEGs using **WP ID mapping**.
- Applied statistical DEG filters in the BLASTp results:
  - `|log2FC| ≥ 1`
  - `p-value < 0.05`

### 4.3 Output

- Pathway-specific DEG tables.
- Heatmaps of log2FC values for fructose- and malate-associated genes.

---

## 5. Network construction and analysis

### 5.1 STRING network and metadata preprocessing

(Implemented in `03.networks.ipynb`.)

- Imported STRING physical interaction files for *C. rodentium*.
- Retained **high-confidence edges** only:
  - `STRING combined_score ≥ 700`
- Mapped STRING protein IDs to **WP IDs** using alias tables.
- Removed duplicated edges and symmetric pairs.
- Merged VF with Fructose/malate-associated DEGs

### 5.2 Network focus (Cytoscape)
- Imported filtered networks into Cytoscape.
- Built an induced **HOP-2 subnetwork**:
  - VF DEGs as seed nodes
  - first- and second-degree neighbors included
- Computed:
  - **Betweenness centrality** (nodes)
  - **Edge betweenness** (edges)
- Community detection:
  - clusterMaker2 / GLay
- Manual pruning of very small components for clarity.

### 5.4 Interpretation

- Network modules represent **cofunctional blocks**.
- High-betweenness nodes act as **potential bottlenecks** connecting metabolism and virulence programs.
- Recurrently highlighted nodes include:
  - NuoC
  - ManX
  - ROD16441
  - Phage-associated proteins

---

## 6. Network visualizations

- The resulting network are provided here: `net1.png` and `net2.png`. Both represent the same filtered interaction network, with different are limited to layout and visualization choices.


### Network legend:
- **Node shape**
  - Triangle = **virulence factor (VF)**
  - Square = **fructose/malate-associated**
  - Circle = other nodes
- **Node size / color**
  - used to improve readability (e.g., centrality-based emphasis)
- **Node border**
  - encodes **log2FoldChange** direction and magnitude (up/down)
- **Edge thickness**
  - EdgeBetweenness: thicker edges = more shortest paths traverse the edge → potential bridges between modules

> Note: centrality mappings are used for visual guidance; the structural “bottleneck” concept is primarily supported by **betweenness** (nodes) and **edge betweenness** (edges).

---

## 7. Conceptual synthesis

This project integrates three analytical layers:

1. **Differential expression** (RNA-seq)
2. **Functional annotation**
   - Virulence (VFDB)
   - Metabolism (KEGG)
3. **Network topology**
   - High-confidence STRING interactions

Together, these layers allow prioritization of genes that:
- are transcriptionally regulated by Compound X,
- participate in virulence or carbon metabolism,
- and occupy structurally important positions in interaction networks.

---

## 8. Status and future directions

- **Pathway enrichment:** GO / KEGG enrichment on the full network or individual modules to identify overrepresented biological processes.
- **Module-level interpretation:** Functional annotation of network communities to distinguish virulence-driven vs metabolism-driven modules.
- **Centrality-based prioritization:** Identification of hub and bottleneck genes (e.g. high betweenness) as key regulatory candidates.
- **Virulence–metabolism coupling:** Assessment of network proximity between virulence factors and fructose/malate metabolism genes.
- **Multi-omics integration:** Integration of fecal metabolomics data to contextualize metabolic modules and refine biological hypotheses.


