# bacterial-metacommunities-of-subtropical-reservoirs
This repository contains all R scripts used to generate the results, figures, and statistical analyses for the manuscript:  “Trophic-state transitions disrupt the coupling between biodiversity and network stability in bacterial metacommunities of subtropical reservoirs”
---
Required input files
The scripts assume that the following input files are available in the working directory. File names can be modified in the scripts if needed.
Community and taxonomy data
`Bacteria.otu.csv`  
OTU/ASV count table with OTUs as rows and samples as columns. A taxonomy column named `taxa` is required for genus-level heatmap and network taxonomy annotation.
`Bacteria.otu.txt`  
OTU/ASV count table used for iCAMP community assembly analysis.
`OTU.match.tre`  
Phylogenetic tree matching the OTU/ASV table, required for iCAMP analysis.

Metadata and environmental data
`sample_metadata.csv`  
Sample metadata table. Required columns include:
`SampleID`
`Season`
`LifeType`
`TSI`
`TrophicState`
`environmental_variables.csv`  
Environmental data table. Required or expected columns include:
`SampleID`
`Season`
`LifeType`
`TrophicState`
`Temperature`
`pH`
`DO`
`ORP`
`Cond`
`SD`
`TN`
`TP`
`NO2_N`
`NO3_N`
`NH4_N`
`Chla`
`TSI`
`wet.season.env.csv` and `dry.season.env.csv`  
Environmental metadata used for SPIEC-EASI network construction. These files should include sample identifiers, lifestyle information, and TSI values.

Network and diversity data
`SPIEC_EASI_site_network_metrics.csv`  
Site-level network metrics used in combined alpha-diversity and biodiversity–stability analyses.
`SPIEC_EASI_site_network_metrics_alpha_diversity.csv`  
Merged table containing network metrics and alpha-diversity indices for biodiversity–stability coupling analysis.
`pairwise_TSI_diff_iCAMP_same_trophicalstate.csv`  
Pairwise iCAMP output table used to compare community assembly process contributions among trophic-state contexts.
---
Required R packages
Install the required packages before running the scripts:
```r
install.packages(c(
  "tidyverse",
  "vegan",
  "broom",
  "ggplot2",
  "openxlsx",
  "segmented",
  "pheatmap",
  "car",
  "igraph",
  "ggraph",
  "ggrepel",
  "ggpubr",
  "rstatix",
  "multcompView",
  "ape",
  "nlme",
  "picante",
  "abind",
  "ecodist",
  "QuantPsyc"
))

# Packages that may need Bioconductor or GitHub installation
if (!requireNamespace("BiocManager", quietly = TRUE)) install.packages("BiocManager")
BiocManager::install("phyloseq")

# SPIEC-EASI and iCAMP may need separate installation depending on your R setup
# install.packages("SpiecEasi")
# install.packages("iCAMP")
```
---
Recommended analysis workflow
The scripts can be run independently, but the following order is recommended for reproducing the full analysis:
1. Environmental comparisons
Run:
```r
source("Environmental variable comparison.R")
```
Main outputs:
`Environmental_variable_statistical_tests.xlsx`
`Environmental_tests_trophic_state.csv`
`Environmental_tests_season.csv`
This step compares environmental variables between trophic states and seasons.
---
2. Ecological threshold, PCoA, and PERMANOVA
Run:
```r
source("Ecological threshold, PCoA, and PERMANOVA analysis.R")
```
Main outputs:
`Segmented_regression_PCoA1_TSI.pdf`
`PCoA_segmented_PERMANOVA_results.xlsx`
`Segmented_PCoA1_TSI_model.rds`
`Bray_Curtis_distance_matrix.rds`
This step estimates the ecological threshold along the TSI gradient and tests the effects of season, lifestyle, and trophic group on community composition.
---
3. Environmental drivers and dominant genus heatmap
Run:
```r
source("Environmental drivers and top 40 genus heatmap.R")
```
Main outputs:
`envfit_environmental_drivers_FDR.csv`
`NMDS_envfit_environmental_vectors.pdf`
`Top40_dominant_genera_heatmap.pdf`
`envfit_and_top40_heatmap_results.xlsx`
This step identifies environmental variables associated with bacterial community variation and visualizes dominant genus-level patterns.
---
4. SPIEC-EASI network construction and site-level topology
Run:
```r
source("SPIEC-EASI network construction and site-level topology.R")
```
Main outputs:
`SPIEC_EASI_site_level_network_metrics.xlsx`
`GLM_TSI_network_metrics.csv`
`SPIEC_EASI_regional_networks.rds`
`SPIEC_EASI_site_metrics.rds`
This step constructs season- and lifestyle-specific bacterial association networks and calculates site-level network properties, robustness, complexity, and stability.
---
5. SPIEC-EASI network visualization
Before running this script, load the regional network object:
```r
network_list <- readRDS("SPIEC_EASI_regional_networks.rds")
source("Visualization of SPIEC-EASI subnetworks.R")
```
Main outputs:
`SPIEC_EASI_network_plots/`
`SPIEC_EASI_hub_taxa_by_season_lifetype.csv`
This step generates Fruchterman–Reingold network plots and exports hub taxa tables.
---
6. Alpha diversity, GLM, and biodiversity–stability coupling
Run:
```r
source("Alpha diversity, GLM, and biodiversity–stability coupling.R")
```
Main outputs:
`GLM_TSI_biodiversity_network_stability.csv`
`GLM_TSI_biodiversity_network_stability.pdf`
`Biodiversity_stability_coupling_GLM.csv`
`Biodiversity_stability_coupling.pdf`
`Alpha_GLM_biodiversity_stability_results.xlsx`
This step calculates alpha diversity and evaluates the relationships among TSI, biodiversity, network topology, network stability, robustness, and complexity.
---
7. Focused biodiversity–stability coupling analysis
Run:
```r
source("Biodiversity–stability coupling analysis.R")
```
Main outputs:
`Biodiversity_stability_coupling_GLM_results.csv`
`Biodiversity_stability_coupling_all.pdf`
`Biodiversity_stability_coupling_plots/`
This step specifically tests whether bacterial diversity predicts network stability, robustness, and complexity within each season × lifestyle × trophic-state context.
---
8. Community assembly process analysis
Run:
```r
source("Bacterial community assembly processes.R")
```
Main outputs:
`icamp.result.csv`
`Assembly_process_boxplots_4panels.pdf`
`Assembly_process_boxplots_4panels.png`
This step estimates bacterial community assembly processes using iCAMP and compares the relative contributions of selection, dispersal limitation, and drift among trophic-state contexts.
---
Key grouping variables
The analyses use three main grouping variables:
`Season`: wet and dry seasons
`LifeType`: free-living bacteria (`FL`) and particle-attached bacteria (`PA`)
`TrophicState`: oligotrophic–mesotrophic reservoirs (`OMR`) and eutrophic reservoirs (`ER`)
Some scripts also use combined trophic-state labels:
Code	Meaning
`WFOM`	Wet-season free-living community in oligotrophic–mesotrophic reservoirs
`WFE`	Wet-season free-living community in eutrophic reservoirs
`WPOM`	Wet-season particle-attached community in oligotrophic–mesotrophic reservoirs
`WPE`	Wet-season particle-attached community in eutrophic reservoirs
`DFOM`	Dry-season free-living community in oligotrophic–mesotrophic reservoirs
`DFE`	Dry-season free-living community in eutrophic reservoirs
`DPOM`	Dry-season particle-attached community in oligotrophic–mesotrophic reservoirs
`DPE`	Dry-season particle-attached community in eutrophic reservoirs
---
Notes on reproducibility
Set the working directory to the repository folder before running scripts:
```r
setwd("path/to/your/repository")
```
Ensure that sample IDs are consistent across OTU tables, metadata tables, environmental tables, and network metric tables.
Several analyses use permutation-based procedures, including PERMANOVA, `envfit`, SPIEC-EASI model selection, and iCAMP randomization. For exact reproducibility, users may add `set.seed()` before these analyses.
SPIEC-EASI and iCAMP analyses can be computationally intensive. Adjust the number of workers, permutations, and randomization times according to available computing resources.
The iCAMP script currently uses:
```r
rand.time = 1000
nworker = 4
bin.size.limit = 48
```
These settings can be modified depending on dataset size and computing capacity.
---
Citation
If you use or adapt these scripts, please cite the associated manuscript:
Ren et al. Trophic-state transitions disrupt the coupling between biodiversity and network stability in bacterial metacommunities of subtropical reservoirs.
---
Contact
For questions about the scripts or manuscript, please contact the corresponding author.
