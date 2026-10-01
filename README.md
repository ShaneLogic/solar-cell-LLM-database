# Solar Cell Simulation Data

An example multilayer-device input and spreadsheet simulation exports for a **Spiro / MAPICl / TiO2** stack. The repository stores tabular artifacts and explanatory slides. It does not contain an LLM, a training pipeline, or the simulation code that generated the workbooks.

## Contents

All data are under [`SpiroMAPIClTiO2/`](SpiroMAPIClTiO2/).

| File | Contents |
| --- | --- |
| [`layer_unit_test.csv`](SpiroMAPIClTiO2/layer_unit_test.csv) | One device configuration: 7 records and 34 fields |
| [Current flux density.xlsx](<SpiroMAPIClTiO2/Current flux density.xlsx>) | 16 current/flux worksheets, each 50 x 480 |
| [Electrostatic potential & Electric field.xlsx](<SpiroMAPIClTiO2/Electrostatic potential & Electric field.xlsx>) | `Fion`, `Vion`, `rho`, each 50 x 481 |
| [Potential & Energy.xlsx](<SpiroMAPIClTiO2/Potential & Energy.xlsx>) | `Ecb`, `Evb`: 50 x 481; `EFn`, `EFp`: 10 x 481 |
| [Recombination.xlsx](SpiroMAPIClTiO2/Recombination.xlsx) | `btb`, `srh`, `vsr`, `tot`, each 50 x 481 |
| [SemiSimu-output.pptx](SpiroMAPIClTiO2/SemiSimu-output.pptx) | Five slides describing output quantities and plotting terminology |

These dimensions count **data**, not a header row. The first row and first column contain values. Some numerical entries are stored as Excel text and others as numeric cells.

## Device input

The CSV describes two electrodes, two transport layers, two interfaces, and one active layer:

```text
electrode -> Spiro -> interface -> MAPICl -> interface -> TiO2 -> electrode
```

It is one configuration, not seven independent device samples. Non-electrode `layer_points` entries are 100, 40, 200, 40, and 100. Their sum is 480, but the actual spatial coordinate vector is not included in the exports.

| Field group | CSV columns |
| --- | --- |
| Geometry and mesh | `layer_type`, `material`, `thickness`, `layer_points`, `xmesh_coeff` |
| Energy parameters | `Phi_EA`, `Phi_IP`, `EF0`, `Et` |
| Densities and ionic ceilings | `Nc`, `Nv`, `Ncat`, `Nani`, `c_max`, `a_max` |
| Mobilities | `mu_n`, `mu_p`, `mu_c`, `mu_a` |
| Dielectric, generation, and recombination parameters | `epp`, `g0`, `B`, `taun`, `taup`, `sn`, `sp`, `vsr_zone_loc` |
| Plot colors | `Red`, `Green`, `Blue` |
| Global/device options | `optical_model`, `xmesh_type`, `side`, `N_ionic_species` |

The first electrode row specifies `Beer-Lambert`, `erf-linear`, `left`, and one ionic species. Many fields are blank where values are not supplied; do not indiscriminately replace those blanks with zero.

Raw thickness values are `2.00E-05`, `2.00E-07`, `3.40E-05`, `2.00E-07`, and `1.00E-05`. **The CSV has no explicit units row or schema.** Do not interpret these values directly as nanometers or assume a conversion without the generating model's conventions.

## Output quantities

The slides reference `dfana.calcr(sol, mesh_option)` and `dfplot` operations. Those functions are not included here. The slides provide context, not an executable interface or a complete machine-readable schema.

| Workbook | Worksheet names |
| --- | --- |
| Current/flux | `Jn`, `Jp`, `Jc`, `Ja`, `Jdisp`, `Jtot`, `Jndrift`, `Jndiffu`, `Jpdrift`, `Jpdiffu`, `Jadrift`, `Jadiffu`, `Jcdrift`, `Jcdiffu`, `Jddisplay`, `Jddtot` |
| Electrostatics | `Fion`, `Vion`, `rho` |
| Energy levels | `Ecb`, `Evb`, `EFn`, `EFp` |
| Recombination | `btb`, `srh`, `vsr`, `tot` |

The presentation associates current terms with electrons, holes, cations, anions, and displacement current. `btb`, `srh`, and `vsr` denote band-to-band, Shockley-Read-Hall, and interfacial surface recombination. `EFn` and `EFp` denote electron and hole quasi-Fermi levels.

Preserve the literal worksheet name **`Jddisplay`** when reading the file. The slides use `Jddisp`, so assuming the slide label is the workbook key will fail.

The slides describe spatial profiles across rows and temporal series down columns. No explicit time, bias, or spatial-coordinate arrays are shipped with the workbooks. The 480/481-column and 10/50-row distinctions must be resolved before aligning, combining, or interpolating quantities.

## Read the data in Python

```bash
git clone https://github.com/ShaneLogic/solar-cell-LLM-database.git
cd solar-cell-LLM-database
python -m pip install pandas openpyxl
```

```python
from pathlib import Path
import pandas as pd

root = Path("SpiroMAPIClTiO2")
layers = pd.read_csv(root / "layer_unit_test.csv")
print(layers[["layer_type", "material", "thickness", "layer_points"]])

book = pd.ExcelFile(root / "Current flux density.xlsx", engine="openpyxl")
print(book.sheet_names)

# No header row: convert numeric strings and reject unexpected text.
jtot = pd.read_excel(book, sheet_name="Jtot", header=None)
jtot = jtot.apply(pd.to_numeric, errors="raise")
assert jtot.shape == (50, 480)

efn = pd.read_excel(root / "Potential & Energy.xlsx", sheet_name="EFn", header=None)
efn = efn.apply(pd.to_numeric, errors="raise")
assert efn.shape == (10, 481)
```

The default `header=0` would silently consume the first data row. Explicit conversion handles the mixed Excel cell types without hiding unexpected strings as missing data.

## Interpretation and reuse

- These are simulation artifacts for one example configuration, not documented experimental measurements or a curated multi-device training benchmark.
- A complete generating-solver revision, run protocol, coordinate vectors, unit schema, and parameter-to-output provenance manifest are not supplied.
- Do not assume that the two total-current representations or matrices with different shapes are interchangeable.
- For ML or LLM-assisted analysis, add verified units, axes, sample/state definitions, provenance, and split rules before assigning scientific targets or making performance claims.
- A dataset license is not declared by a standalone license file in this snapshot.
