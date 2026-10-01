# Forest Stand Reconstruction

> **Not intended for end users.** This repository holds the exploratory code, simulations and validation experiments used while developing the **[standrecon](https://github.com/kriggithub/standrecon)** R package. To reconstruct forest stands, use standrecon itself, available on CRAN: <https://CRAN.R-project.org/package=standrecon>.
>
> ```r
> install.packages("standrecon")
> ```

![Corwina CPP basal area, measured vs. reconstructed](corwinaCPP_BA.png)

*Corwina CPP plots: measured basal area in 2023 next to the 1912 reconstruction under the 25%, 50% and 75% decomposition percentiles, split by species (PIPO, PSME). Produced by `corwinaPlotting.R`.*

## What's here

The scripts trace how the reconstruction method went from a hard-coded, single-dataset workflow to the general `standRecon()` function:

1. **Prototype workflow** (`liveTrees.R`): steps through the method by hand for two species (ABBI, PIEN) and one reference year (1960):
   - estimate missing live-tree ages from per-species Age ~ DBH linear models
   - subtract annual radial growth to get DBH at the reference year
   - assign dead trees to condition classes from status and decay codes
   - estimate time since death with Fulé's decomposition equation at the 25th, 50th and 75th percentiles
   - apply a bark correction to dead trees in condition classes 5 to 7
   - sum basal area and stem density
2. **Modular functions** (`ForestReconstructionFunc.R`): splits the steps into separate functions (`ageLM`, `refLiveDBH`, `conclass`, `decompRate`, `addBark`), then combines them into `forestStandReconstruction()`.
3. **Early full function on the GUMO data** (`GUMOtest.R`): runs the modular steps and a first version of the full function on GUMO (reference years 1922 and 1975) and Alpine (1960 and 1975).
4. **Multiple reference years** (`refYears.R`): `forestStandReconstruction()` with a vector of reference years, a minimum-DBH cutoff, plot-size scaling and a default lookup of species-specific bark equations. It is run on yearly series for Alpine, GUMO and both Corwina datasets. `initialPlots.R` plots density and basal area against reference year.
5. **Current function** (`standRecon.R`, `helperfunc.R`): `standRecon()` takes column names as strings and adds an `nPlots` argument. It returns a single data frame of reconstructed and measured basal area and stem density by species, year and percentile. Helpers: `liveTreeAge()`, `fuleDecomp()` and `createConclass()`.
6. **Validation runs and figures** (`standReconTests.R`, `corwinaPlotting.R`): run `standRecon()` on the Corwina data (measured 2023, reference year 1912) and produce the result tables and the basal-area and density bar charts.

## Data

| Dataset | Raw files | Cleaned file | Species retained | Measurement year in code |
|---|---|---|---|---|
| Alpine | `tree_data.csv`, `dead_tree_data.csv` (transect/plot layout) | `AlpineFullTreeData.csv` | ABBI, PIEN | 2013 |
| GUMO | `GUMOliveTrees.csv`, `GUMOdeadTrees.csv` | `GumoFullTreeData.csv` | PIED, PIPO, PIST, PSME, QUGA | 2004 |
| Corwina (C-2 and CPP plots) | `corwinaData.csv`, `corwinaAgeData.csv` (cores with adjusted ages) | `corwinaC2Data.csv`, `corwinaCppData.csv` | PIPO, PSME | 2013 (`refYears.R`), 2023 (`standRecon` runs) |

`dataCleaning.R` builds the cleaned files from the raw ones. For Corwina, it drops plot C-1 and splits the remaining plots into C-2 and the other plots (CPP).

The repository does not spell out the site names. The raw GUMO sheets already contain spreadsheet reconstructions for 1922, and the Alpine prototype says its average increments came from "Datasheets for RMBL Reconstruction".

## Repository contents

| File | Description |
|---|---|
| `standRecon.R` | Current `standRecon()` function |
| `helperfunc.R` | Helpers: `liveTreeAge()`, `fuleDecomp()`, `createConclass()` |
| `refYears.R` | `forestStandReconstruction()` over many reference years, with example runs |
| `ForestReconstructionFunc.R` | Step-by-step modular functions and the first combined function |
| `GUMOtest.R` | Early tests on the GUMO and Alpine data |
| `liveTrees.R` | Original hard-coded prototype (Alpine, 1960); `liveTrees.RData` is its saved workspace |
| `standReconTests.R` | `standRecon()` runs on Corwina, plus result tables and base-R plots |
| `corwinaPlotting.R` | ggplot basal-area and density figures for Corwina |
| `initialPlots.R` | Plots of density and basal area against reference year |
| `dataCleaning.R` | Builds the cleaned Alpine, GUMO and Corwina datasets |
| `*.csv` | Raw and cleaned tree data (see [Data](#data)) |
| `corwina*_BA.png`, `corwina*_Density.png`, `plots/` | Output figures and tables |

## Author

**Kurt Riggin**: [GitHub](https://github.com/kriggithub) · [ORCID](https://orcid.org/0009-0004-4700-1251)
