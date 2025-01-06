# NIPMAP

Thus document aims to summarise the result of a 2-month computational internship. The contents are:
- NIPMAP results reproduction with MIBI images of TNBC of Keren et al (2021)
- re-application of NIPMAP on the bone cancer dataset (from Lukas Hatscher)
- interpretation of the method output & conclusions

## Proof of concept: 
ran NIPMAP on the original data with the GITHUB repo cloned: 
maybe compare the results to the original ones? i dunno. have to run it again i guess, cause my dumbass forgot to save any outputs... 

### Data: 
regionprops_annotated - csv files with cell coordinates in images, 1000x1000 px, 1000x1000µm
intensities - csv files with marker intensities in images 

─ 7n_output
│   ├── AA_sites.json
│   ├── ca_sites.json
│   ├── cells_niches.json
│   ├── params.json
│   ├── pca_sites.json
│   └── sites_cells_archs.csv
├── TMENS_analysis
│   └── data
│       ├── cellData.csv
│       └── cell_positions_data



process: 
- produce csvs from the ann data object: two folders (intensities & regionsprops) with csv files for each patient, sample and ROI. names in format: TS-373_IMC78_UB_001.csv (TS-373_IMC*patient ID*_*disease*_*ROI*.csv)
- intensities folder contains files for each patient ID with phenotypic markers and marker intensities for each cell (object).
- regionsprops folder contains files for each patient, with X and Y coordinates and phenotypes for each cell (object)
- the script preprocessing.py will rename columns as in the NIPMAP tutorial (https://github.com/jhausserlab/NIPMAP/blob/main/README.md#inputs) and create a main 

parameters to change: 
- protein markers
- cell types (phenotypes)
- number of niches
- radii of sites
- number of sites in the script 


# README

NIche Phenotype MAPping (NIPMAP) analysis from spatial multiplex data.
Publication describing the method: El Marrahi et al., Nature Communications 2023 https://www.nature.com/articles/s41467-023-42878-z

The spatial architecture of tumors has high relevance for diagnostic and therapy and can be surveyed by multiplex histology techniques such as imaging mass cytometry and multiplex immunofluorescence. These techniques produce spatial maps of dozens to hundreds of cellular and phenotypic markers. Surveying these spatial maps exhaustively requires browsing through 10’000+ images per sample. To address this, NIPMAP uses unsupervised machine-learning to (1) concisely and accurately summarize the architecture of tissues, and (2) automatically identify phenotypes with salient spatial architecture spheroids.

Data: Multiplex Ion Imaging on 41 Triple Negative Breast tumors from [Keren et al, Cell(2018)](10.1016/j.cell.2018.08.039) and In Situ Sequencing data on human lung development from [Sountoulidis et al, 2022](https://doi.org/10.1101/2022.01.11.475631)

## Prerequisites

Tested on: OS: Red Hat Enterprise Linux 8.10 (Ootpa)

minimal_env.yml 

* Python 3.7.13 installed, together with these libraries:
    ```bash
    pip install matplotlib
    pip install scipy
    pip install pandas==1.3.5
    pip install numpy==1.8.1
    pip install scikit-learn
    pip install seaborn
    pip install qpsolvers==1.9.0
    pip install mpl_toolkits
    ```
* R 4.1.3 with RStudio installed, together with these libraries:
    ```
    pkgs <- c("tidyverse","ggplot2","ade4","factoextra","plotly","igraph","reshape2","ggrepel","viridis","fdrtool","pheatmap","cluster","broom","pROC","ggpubr","devtools","ggridges")
    install.packages(pkgs)
    ```

## Quick start


### data: 
- updated_csvs/ in there: intensities & regionprops
- process:

produce csvs from the ann data object: two folders (intensities & regionsprops) with csv files for each patient, sample and ROI. names in format: TS-373_IMC78_UB_001.csv (TS-373_IMCpatient IDdiseaseROI.csv)
intensities folder contains files for each patient ID with phenotypic markers and marker intensities for each cell (object).
regionsprops folder contains files for each patient, with X and Y coordinates and phenotypes for each cell (object)
the script preprocessing.py will rename columns as in the NIPMAP tutorial (https://github.com/jhausserlab/NIPMAP/blob/main/README.md#inputs) and create a main

### Inputs
1. One CSV file per sample named *patient\<Patient ID\>_cell_positions.csv*, with cells as rows and the following columns:
* *x*, x position of the cell in the image
* *y*, y position of the cell in the image
* *label*, an integer index for the cell, to cross reference with the *cellData.csv* file (below)
* *cell_type*, a string describing the cell type
2. One CSV file named *cellData.csv* with cells as rows and the following columns:
* *cellLabelInImage*, an integer index for the cell, to cross reference with the *patient\<Patient ID\>_cell_positions.csv* files (above)
* *SampleID*, the sample of origin, matching the *\<Patient ID\>* of the *patient\<Patient ID\>_cell_positions.csv* files
* one column per marker containing the intensity of each marker (for example MHCI, CD68, CD45RO, ...)
The repository contains example CSV files from the Keren et al. and Sountoulidis et al. studies.

### Performing the niche-phenotyping analysis
1. Open *main_nipmap.py*. Set the parameters in the script header. Execute the script in Jupyther or on the command line:
 ```bash
 python3 main_nipmap.py
 ```

### parameters to choose: 

This script will generate these outputs as json files:
* Generation of sites and cellular abundances within them (+ radius size selection)
* PCA and Archetype Analysis
* Niche identification
* Niche segmentation of images
2. Open *py_wrapper_nipmap.r* script. Set the parameters in the script header. Execute the script in Rstudio or on the command line:
  ```bash
  R --vanilla < py_wrapper_nipmap.r
  ```
  This script produces these outputs including figures: 
* Niche-phenotype mappping

Run time is around 20 min for one sample and around 60 min for a 40-patient sample MIBI dataset.

Note: one sample = one image
Note #2: NIPMAP doesn't aim to correct cell segmentation error or cell type mis-assignments, this needs to be addressed prior to niche-phenotype mapping. 


### outputs 

### results / interpretation / scatterplots 



## FILES

```
└───/main_nipmap.py: calls py functions for NIPMAP pipeline + saves outputs into .json files
└───/py_wrapper_nipmap.r: get NIPMAP outputs from .json files and loads R objects for further analyses
└───/README.md
|
/ISS_analysis
|
└───/data
|
└───/notebooks
|
└───/output
|
└───/scripts
/macro_niches_analysis
|
└───/data
|
└───/figs
|
└───/outputs
|
└───/scripts
/phenotype_niches
|
└───/data
|
└───/figs
|
└───/outputs
|
/TMENS_analysis
|
└───/clustering_MIBI_data.py: clustering (k-means) of sites cell abundance compared with niche-finding in NIPMAP
└───/building_blocks_mapping.Rmd: Marker expression analysis in niches
└───/data
|   |   cellData.csv: normalized data (markers intensity) from CyTOF expfrom Keren et al.,Cell(2018)
|   |
|   └───/cell_positions_data: .csv files for that contain for each patientx,y positions and label of cells from Keren et al.,Cell(2018)
|   |
|   └───/keren_building_blocks_analysis
|   |   |   archetype_analysis.ipynb
|   |   |   boundaries_analysis.ipynb
|   |   |   cell_positions_visualization.ipynb
|   |   |   site_radius_analysis.ipynb
|   |   |   sites_patient_mapper.ipynb
|
└───/output
| 
└───/src
    |	CellAbundance.py
    └───/utils
        |   archetypes.py
        |   equations.py
        |   visualization.py
/mixed_compartmantalized_analysis
|
└───/data: .json output files from NIPMAP
|
└───/out: .figures generated by the analysis
|
└───/notebooks:
    |    mixed_compartmantalized_scoring.Rmd
|
```

## Reproducing analysis from El Marrahi et al.
* **Niches identification**: Open TMENS_analysis/notebooks/keren_building_blocks_analysis/sites_radius_analysis.ipynb and execute the chunks to select the radius of sites that allows to explain the most the covariance structure of the dataset. Open TMENS_analysis/notebooks/keren_building_blocks_analysis/archetype_analysis.ipynb and excute "3D Archetypes- All tumors- 4 archetypes", "Visualization and interpretation of archetypes" and "Export data from PCA and Archetype analysis" sections. Open in TMENS_analysis/keren_building_blocks_analysis sites_patient_mapper.ipynb and execute each chunk and name .csv files
* **Images segmentation into niches**: Open TMENS_analysis/notebooks/cell_positions_visualization.ipynb
* **Niche-phenotype mapping**: Open and excute this file from phenotypes_niches: niche_phenotype_mapping.Rmd. Figures are found in /phenotypes_niches/figs
* **Comparing NIPMAP and clustering approaches in niche identification**: run TMENS_analysis/clustering_MIBI_data.py script
* **Macroscopic analysis of niches from CyTOF data (Wagner et al,2019)**: Open and excute the following R scripts from /macro_niches_analysis folder: 1. Processing of CyTOF data: scBC_analysis.Rmd, 2.Macro-microscopic cell composition of tumors mapping:  scBC_newCells.Rmd, 3. Linear regression of macroscopic cellular abundance over niches: lm_TMENS.Rmd Figures are found in /figs folder from /macro_niches_analysis
* **NIPMAP on ISS dataset from Sountoulidis et al**: Open ISS_analysis/notebooks/archetype_analysis.ipynb and excute "Radius Analysis", "Archetype Analysis", "Visualization" and "Save data". The "Save data" part generates all the files needed for downstream analyses. Then, open ISS_analysis/notebooks/HybISS_niche_explore.Rmd and excute the whole Rmarkdown file to generate all the figures included in the section "NIPMAP identifies the cellular and phenotypic architecture of developing lung from in situ RNA sequencing" of the paper. Figures generated for this analysis are found in ISS_analysis/output.

## Supplementary analyses
* **Sampling intensity and niche estimation error** see script in /TMENS_analysis/sites_analysis.py. The script computes RMSE for each sampling intensity
* **Comparing niches in fetal lung with 32 vs 73 cell types** see notebook in /ISS_analysis/notebooks/niches_ISS_original_celltypes.ipynb. It performs niche identification from images with 73 cell types. Figures are available in /ISS_analysis/output.
* **Power analysis - detecting a rare niche** see scripts in /power_analysis/. T
* **Reproducing mixed vs compartmentalized sample classification of Keren et al.** open and execute this script: mixed_compartmantalized_scoring.Rmd

## License

GNU General Public License version 3

## Contact
Anissa El Marrahi - <anissa.el@scilifelab.se>   <anissel12@gmail.com>
Ziqi Kang - <ziqi.kang@scilifelab.se>
Jean Hausser - <jean.hausser@scilifelab.se>

## Acknowledgments
Fabio Lipreri
David Alber
