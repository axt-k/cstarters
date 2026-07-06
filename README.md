# Cheminformatics Analysis of Lipopeptide Starter Structures

This directory contains the cheminformatics pipeline for characterising the structural diversity of lipopeptide starter units incorporated through condensation starter (C\_starter) domains. The analysis corresponds to **Figure 5** of the manuscript and covers backbone saturation, chain length distribution, position-resolved functional group profiling, and principal component analysis (PCA) of Morgan fingerprints.

---

## Overview

The script `cstarter_cheminformatics_analysis.py` reads a curated dataset of lipopeptide starter structures (as SMILES) along with their category labels and produces a multi-panel figure summarising:

| Panel | Description |
|---|---|
| **Bond saturation** | Single vs. double bond counts at each backbone carbon position across all aliphatic starters. |
| **Backbone length distribution** | Histogram of aliphatic starter chain lengths (number of carbons). |
| **Functional group indexing** | Stacked bar charts of functional groups (carboxylic acid, hydroxyl, methyl, epoxide, ketone, amine, imine) along the backbone for each size class (SCFA, MCFA, LCFA), with an overlaid line showing the number of starters that extend to each position. |
| **PCA of starter fingerprints** | Morgan fingerprints (radius 2, 2048 bits) reduced to two principal components, coloured by category (AR, SCFA, MCFA, LCFA). A small jitter is added to separate overlapping points. |

Additionally, the script logs the five most common aromatic starter structures (by InChIKey) with their counts and average PCA coordinates, and exports all PCA coordinates with SMILES to a CSV file.

### Starter unit categories

| Category | Definition |
|---|---|
| **AR** | Aromatic — contains any aromatic moiety |
| **SCFA** | Short-chain fatty acid — &le;6 carbon atoms |
| **MCFA** | Medium-chain fatty acid — 7&ndash;9 carbon atoms |
| **LCFA** | Long-chain fatty acid — &ge;10 carbon atoms |

---

## Directory contents

```
cheminformatics/
├── cstarter_cheminformatics_analysis.py   # Main analysis script
├── environment.yml                        # Conda environment specification
├── ground_truth_curated.csv               # Curated input dataset
├── functional_groups.cdxml                # ChemDraw file of functional group definitions
├── notes                                  # Development notes
├── log.txt                                # Example log output from a previous run
├── cstarter_cheminformatics_analysis.svg  # Example output figure (SVG)
├── output/                                # Default output directory
│   ├── cstarter_cheminformatics_analysis.svg
│   ├── cstarter_cheminformatics_analysis.png
│   ├── starter_pca_plot.png               # Standalone PCA scatter plot
│   └── pca_coordinates_with_smiles.csv    # PCA coordinates mapped to SMILES & categories
└── README.md                              # This file
```

---

## Environment setup

A Conda environment file is provided. Python 3.12 is used.

```bash
# Using mamba (recommended):
mamba env create -f environment.yml
mamba activate cstarter

# Or using conda:
conda env create -f environment.yml
conda activate cstarter
```

## Usage

```bash
python cstarter_cheminformatics_analysis.py \
    --data ../dataset.csv \
    --out-dir output/
```

### Command-line arguments

| Argument | Short | Default | Description |
|---|---|---|---|
| `--data` | `-d` | *(required)* | Path to the input CSV file |
| `--out-dir` | `-o` | *(required)* | Directory for output figures and CSV |
| `--cname-starter-smiles` | `-s` | `starter_canonical_smiles` | Column name for starter SMILES |
| `--cname-starter-category` | `-c` | `starter_category` | Column name for starter category (AR / SCFA / MCFA / LCFA) |
| `--cname-genus` | `-g` | `ncbi_organism_genus` | Column name for producing organism genus |
| `--cname-species` | `-p` | `ncbi_organism_species` | Column name for producing organism species |
| `--log-level` | `-l` | `INFO` | Logging verbosity (DEBUG, INFO, WARNING, ERROR, CRITICAL) |
| `--log-file` | `-f` | `None` | Optional path to write log output to a file |

### Input data requirements

The CSV must contain at minimum:

- `ncbi_organism_superkingdom` — used to filter for Bacteria
- The SMILES column (default `starter_canonical_smiles`) — valid SMILES that parse with RDKit and contain a COOH group
- The category column (default `starter_category`) — one of `AR`, `SCFA`, `MCFA`, `LCFA`
- Genus and species columns

Records that fail SMILES parsing or lack a COOH group are skipped with a warning.

### Outputs

| File | Description |
|---|---|
| `cstarter_cheminformatics_analysis.png` | Multi-panel figure at 600 DPI |
| `cstarter_cheminformatics_analysis.svg` | Vector version of the same figure |
| `starter_pca_plot.png` | Standalone PCA scatter plot (all points in black, no category colouring) at 300 DPI |
| `pca_coordinates_with_smiles.csv` | Columns: `SMILES`, `Category`, `PC1`, `PC2` |
