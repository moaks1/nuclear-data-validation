# Nuclear Data Verification for the Python Activation Code

This repository contains reproducible inspection notebooks used to substantiate specific nuclear-data statements made in the NWAC manuscript. The raw evaluated nuclear-data files are **not redistributed in this repository**; users must obtain their own copies from the original library sources.

The repository is intentionally narrow in scope. It is **not** the Python Activation Code itself and it does not perform neutron transport or Monte Carlo calculations. Its purpose is to provide a transparent and reproducible record of the code used to inspect specific evaluated nuclear-data files.

## Current verification

The current notebook examines the evaluated incident-neutron data for the following statement concerning the \(^{24}\mathrm{Mg}(n,p)\) reaction and production of metastable \(^{24}\mathrm{Na}\):

> Inspection of the evaluated \(^{24}\mathrm{Mg}\) incident-neutron files showed that JEFF-4.0 does not provide a state-resolved production entry for metastable \(^{24}\mathrm{Na}\) associated with MT=103, whereas EAF-2010 provides explicit metastable-state production data.

The notebook checks the original ENDF-formatted evaluations directly using the OpenMC Python API.

## Repository layout

```text
.
├── README.md
├── requirements.txt
├── MF10_MT103.ipynb
└── data/
    ├── JEFF-4.0/
    │   └── <place your downloaded Mg-24 ENDF file here>
    └── EAF-2010/
        └── <place your downloaded Mg-24 ENDF file here>
```

The raw JEFF-4.0 and EAF-2010 evaluated nuclear-data files are **not included in this repository**. To reproduce the analysis, users must independently obtain the corresponding Mg-24 evaluation from the original library source and place one file in each directory shown above.

The files used in the original analysis were:

```text
data/JEFF-4.0/n_12-Mg-024g.jeff
data/EAF-2010/Mg24.end
```

The notebook automatically finds the single non-hidden file in each library directory, so the downloaded files may retain their original filenames. Each directory must contain exactly one Mg-24 evaluation file when the notebook is run.

## What the notebook checks

For each evaluated Mg-24 file, the notebook:

1. Loads the original ENDF evaluation with `openmc.data.endf.Evaluation`.
2. Checks whether `(MF, MT) = (9, 103)` and `(10, 103)` are present.
3. Parses each available MF=9, MT=103 final-state subsection.
4. Parses each available MF=10, MT=103 final-state subsection.
5. Prints the product identifier `IZAP`, final-state index `LFS`, Q values, and tabulated branching fractions or production cross sections.
6. Checks specifically for a Na-24 state with:
   - `IZAP = 11024`
   - `LFS > 0`
7. Prints a final concise summary for JEFF-4.0 and EAF-2010.

Under the ENDF-6 format, MF=9 contains multiplicities for radioactive-nuclide production and MF=10 contains radioactive-nuclide production cross sections. Within these sections, `LFS` identifies the final state of the product nucleus.

## Running the notebook

OpenMC is most easily installed through `conda-forge`.

```bash
conda create -n nuclear-data-check -c conda-forge openmc python
conda activate nuclear-data-check

python -m pip install -r requirements.txt
jupyter lab MF10_MT103.ipynb
```

Run all cells from top to bottom.

Before pushing the notebook to GitHub, run it once and save it so that the printed output is embedded in the notebook. This allows readers to see the verification results directly on GitHub without running the notebook themselves.

## Data handling

The notebook reads the evaluated nuclear-data files directly and does not modify them.

### Raw nuclear data are not redistributed

This repository intentionally does **not** include copies of the JEFF-4.0 or EAF-2010 raw evaluated nuclear-data files. These data originate from external nuclear-data libraries and remain subject to the terms and conditions of their respective providers.

Users wishing to reproduce the verification must download their own copies of the Mg-24 evaluations from the appropriate original library sources and place them in:

```text
data/JEFF-4.0/
data/EAF-2010/
```

The notebook will then inspect those local files directly.

For reproducibility, the original filenames should be retained whenever possible. The files used for the manuscript verification were:

```text
JEFF-4.0: n_12-Mg-024g.jeff
EAF-2010: Mg24.end
```

No JEFF-3.3 data are used or required by this repository.

## Software

The inspection uses the OpenMC Python nuclear-data interface, specifically:

```python
openmc.data.endf.Evaluation
openmc.data.endf.get_head_record
openmc.data.endf.get_tab1_record
```

No OpenMC particle-transport calculation is performed.
