# Network legend

**Nodes (proteins)**
- **Triangle (▲)**: Virulence factor–associated genes (VF DEGs; VFDB-based annotation)
- **Square (■)**: Metabolite-related genes (fructose/malate-associated; KEGG-based selection)
- **Circle (●)**: Other proteins in the STRING-induced neighborhood

**Node color**
- Encodes relative importance for visualization purposes (e.g. centrality-based scaling);
  darker tones indicate higher topological relevance within the network.

**Node border**
- Indicates transcriptional regulation:
  - Red border: upregulated (positive log2FoldChange)
  - Blue border: downregulated (negative log2FoldChange)

**Edges (protein–protein associations)**
- Represent high-confidence STRING physical interactions
- Filtered by **combined_score ≥ 700**

**Edge thickness**
- Proportional to **Edge Betweenness Centrality**
- Thicker edges highlight potential bridges connecting functional modules

**Network scope**
- Induced subnetwork centered on virulence-associated DEGs
- Expanded up to **2 interaction hops (HOP-2)** to capture functional context

