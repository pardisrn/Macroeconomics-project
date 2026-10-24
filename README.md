# Iran's Energy Subsidy Reform: Replication and Continuous-Dose Extension

A collaborative macroeconomics course project examining manufacturing performance around Iran's 2010 energy subsidy reform using panel-data econometrics in Python.

The project examines how Iran's 2010 energy subsidy reform relates to manufacturing performance through panel-data analysis and a continuous-dose extension comparing direct energy exposure with dependence on intermediate inputs. The accompanying Persian report discusses industrial technology, capital intensity, exposure, and adjustment capacity.

## Research questions

- How did outcomes change for more energy-sensitive industrial units relative to less sensitive units after the reform?
- How do estimates change when exposure is measured continuously rather than through a binary treatment indicator?
- Are direct energy-cost exposure and dependence on intermediate inputs associated with different post-reform outcomes?
- How sensitive are conclusions to fixed effects, sample restrictions, and the level of clustering?

This repository documents a course project. It is separate from the author's undergraduate thesis and uses econometric methods rather than a machine-learning pipeline.

## Data

The supplied `mydata.xlsx` contains **5,636 observations**, **54 columns**, and **1,336 panel units**, covering **2009–2013**. The panel units are **four-digit industry–province groups**, not individually observed firms. The sample is unbalanced, and estimation samples differ by outcome and specification.

The workbook is a **processed dataset**: it contains logarithmic measures, treatment and classification variables, and fields retained from earlier processing. It should not be described as raw establishment-level data.

| Variable group | Examples |
| --- | --- |
| Panel identifiers | `id`, `year`, `isic`, `isic2`, `province` |
| Main outcomes | `lrouttot` (output), `lrmuvaluad` (value added), `lnmuopsur` (operating surplus) |
| Input and adjustment measures | `lrenerg`, `lrcapform`, `lrperwage`, `lrinpraw`, `lrinppack`, `lrinpndur`, `lrinpmus`, `firmno` |
| Treatment and classifications | `treat`, `tech`, `energcat`, `presnt` |
| Other controls and derived fields | Energy-price, capital-ratio, macroeconomic, interaction, and prior estimation fields |

Keep the supplied workbook unchanged for this notebook. Removing derived columns can break the required-column checks or replication specification. Variable names and transformations follow the supplied project dataset; the notebook is the reference for their operational use.

The data are used in the replication workflow associated with the paper below. Redistribution terms and the precise upstream data-access route are not established by this README. If the workbook cannot be redistributed, users must obtain an authorized copy named `mydata.xlsx` before running the notebook.

## Methodology

### 1. Statistical engine

The first notebook section defines a custom `stats_lite` engine using NumPy, pandas, and mpmath. It implements within-transformed OLS, absorbed unit fixed effects, clustered covariance estimation, an `areg`-style small-sample adjustment, probability calculations, and Wald tests.

### 2. Paper replication

The replication section translates the original Stata-style regression workflow into Python using statsmodels and SciPy. It produces results organized as Tables 5–8 and appendix tables A.3, A.5, and A.6, including main effects, coefficient tables, model information, and joint tests.

The notebook's three internal components are labeled `stats_lite.py`, `codes_final_python.py`, and `extension_analysis.py`. They are incorporated into the notebook; these labels do not imply that three separate Python files need to be downloaded.

### 3. Continuous exposure measures

The extension builds exposure measures from pre-reform observations in **2009–2010**:

- **Direct exposure:** mean energy-cost share of output.
- **Indirect exposure proxy:** mean combined materials, packaging, and tools cost share of output.

The measures are winsorized, standardized, and rank-transformed for alternative specifications. The indirect measure captures dependence on intermediate inputs; it does **not** directly measure suppliers' own energy intensity or identify an upstream energy transmission channel.

### 4. Extension specifications

| Analysis | Purpose |
| --- | --- |
| Specification ladder | Compare the binary treatment estimate across control and fixed-effect choices |
| Continuous-dose DiD | Interact the post-reform indicator with standardized direct exposure |
| Rich fixed effects | Compare unit/year, unit/industry-by-year, and unit/industry-by-year/province-by-year structures |
| Event study | Examine annual exposure-related differences, using 2010 as the reference |
| Direct–indirect comparison | Include both exposure measures and test coefficient differences |
| Adjustment margins | Examine energy inputs, capital formation, wages, materials, firm counts, and manufacturing services |
| Robustness checks | Compare restricted samples, complete pre-period coverage, and rank-based exposure |

Inference comparisons retain the preferred point-estimate specification while changing covariance estimation: clustering by panel unit, clustering by two-digit industry, two-way industry/province clustering, and a null-imposed wild cluster bootstrap-t with **999 replications**. The random seed is **20260815**.

## Selected results from the supplied workbooks

These figures summarize existing saved outputs. They have not been independently re-estimated in a fresh execution during preparation of this README.

### Binary-treatment replication gate

The saved gate reports a pass for the three Table 5, Panel C comparisons against the rounded coefficient and standard-error targets encoded in the notebook.

| Outcome | Coefficient | Standard error | Exact percentage transformation |
| --- | ---: | ---: | ---: |
| Output | -0.0341 | 0.0168 | -3.35% |
| Value added | -0.0758 | 0.0293 | -7.30% |
| Operating surplus | -0.0957 | 0.0509 | -9.12% |

The percentage transformation is `100 * (exp(coefficient) - 1)`. These are relative binary-treatment estimates within the regression design, not the effect of the national reform compared with a no-reform counterfactual. Passing the gate checks these targeted comparisons; it does not validate every replication table or the identifying assumptions.

### Continuous-dose extension

In the preferred fixed-effect specification, the output coefficient on `Post × DirectDose_z` is approximately **0.051 log points per one-standard-deviation increase in pre-reform direct exposure**. Its p-value is approximately **0.015** with unit clustering, **0.194** with industry clustering, **0.222** with two-way clustering, and **0.009** with the implemented wild cluster bootstrap.

The coefficient's interpretation differs from the binary-treatment estimate. It describes a conditional exposure gradient, and statistical conclusions depend on inference choices. It should not be presented as evidence that greater energy exposure caused higher output or that the reform benefited manufacturing overall.

## Repository layout

| Path | Content |
| --- | --- |
| `README.md` | Project description and execution instructions |
| `requirements.txt` | Python package dependencies |
| `macro_econ_project_notebook_FINAL_SUBMISSION.ipynb` | Complete replication and extension notebook |
| `mydata.xlsx` | Processed input workbook, if redistribution is permitted |
| `reports/macroeconomics_project_report.pdf` | Persian project report |
| `results/python_regression_results.xlsx` | Saved replication results |
| `results/extension_results.xlsx` | Saved extension results and diagnostics |

The report path assumes the original `MacroEconMinor_Project_Group16_revised.pdf` has been renamed to `macroeconomics_project_report.pdf`, as recommended for publication. Keep the notebook and input workbook together in the repository root.

## Installation and execution

Use a Python environment compatible with the packages in `requirements.txt`. Exact package versions have not been pinned to a freshly verified environment.

From the project directory:

```bash
python -m venv .venv
```

Activate the environment on Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Or on macOS/Linux:

```bash
source .venv/bin/activate
```

Install dependencies and launch Jupyter:

```bash
python -m pip install -r requirements.txt
jupyter notebook
```

Then:

1. Open `macro_econ_project_notebook_FINAL_SUBMISSION.ipynb`.
2. Confirm that `mydata.xlsx` is in the working directory.
3. Restart the kernel and run all cells from top to bottom.
4. Inspect the data audit and replication gate before interpreting extension outputs.
5. Allow the bootstrap section to finish; it can take longer than individual regressions.

The notebook includes reference command-line functions, but the intended execution route here is the notebook itself. No Stata installation or optimization solver is required by the supplied Python workflow.

## Generated outputs

Execution writes these workbooks to the working directory:

- `python_regression_results.xlsx`: main effects, all coefficients, model information, Wald tests, metadata, and table-specific sheets.
- `extension_results.xlsx`: data audit, replication gate, specification comparisons, dose regressions, event study, inference comparisons, direct–indirect tests, adjustment margins, robustness checks, metadata, and console log.

It also saves:

| Filename | Figure |
| --- | --- |
| `fig1_event_study.png` | Event-study coefficients |
| `fig2_dose_response.png` | Descriptive exposure–response scatter |
| `fig3_adjustment_margins.png` | Adjustment-margin coefficient plot |
| `fig4_inference.png` | Inference-sensitivity p-values |

The `results/` folder stores the published workbook copies. The current notebook does not automatically write to that folder. After rerunning, review the generated outputs and copy the desired versions into `results/`. Generated root-level files with matching names may be overwritten on subsequent runs.

## Reproducibility and interpretation

- The extension audits required variables, year coverage, duplicate `id`–`year` observations, identifier encoding, and pre-period coverage. It stops if the audit or replication gate fails.
- Only two pre-reform years are available. The event study has one pre-reform contrast relative to 2010, providing a limited diagnostic rather than a comprehensive parallel-trends assessment.
- Aggregated industry–province observations cannot identify firm-level technology or capital effects.
- Exposure proxies, sample selection, concurrent macroeconomic shocks, and inference choices limit causal interpretation.
- The saved audit reports 5,426 observations in the dose-restricted sample, compared with 5,636 in the full dataset. Outcome-specific missingness further changes regression samples.
- Existing outputs document a previous run. A fresh top-to-bottom execution and output comparison remain necessary to verify reproducibility in a new environment.

