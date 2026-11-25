<h1 align="center">Make BEM model</h1>

<p align="center">
  Builds a BEM conductor model for MEG/EEG forward modelling from a FreeSurfer subject with MNE-Python, running the watershed algorithm if BEM surfaces are missing, with QC figures and a report.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/brainlife.app.899"><img src="https://img.shields.io/badge/Brainlife-brainlife.app.899-blue.svg" alt="Run on brainlife.io"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-see_LICENSE-lightgrey.svg" alt="License"></a>
</p>

## At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Builds a BEM conductor model for MEG/EEG forward modelling from a FreeSurfer subject with MNE-Python, running the watershed algorithm if BEM surfaces are missing, with QC figures and a report.**

If inner skull, outer skull and outer skin surfaces are not in the subject's bem/ folder, runs mne watershed_bem; then calls mne.make_bem_model with 1 or 3 layers and the chosen ico subdivision and mne.make_bem_solution, saving the solution with a QC thumbnail and an interactive HTML report. Use 3 layers for EEG or combined MEG+EEG and 1 layer for MEG only. Needs a FreeSurfer license.

| | |
|---|---|
| **Inputs** | `neuro/freesurfer` |
| **Outputs** | `neuro/meg/fif` · `report/html` |
| **Container** | `brainlifemeeg/mne-freesurfer:1.12.1-7.4.1` |
| **Resources** | 1 CPUs · 16 GB memory · 2 h walltime |
| **Requires** | `FREESURFER_LICENSE`, `FS_LICENSE` |
| **Run it** | [doi.org/10.25663/brainlife.app.899](https://doi.org/10.25663/brainlife.app.899) |

#### Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `freesurfer` | `neuro/freesurfer` | yes | FreeSurfer recon-all subject directory. |

#### Outputs

| Output | Datatype | Description |
|---|---|---|
| `out_dir` | `neuro/meg/fif` | BEM conductor model solution (meg.fif) for forward modelling. |
| `out_report` | `report/html` | Interactive HTML report with BEM contours over MRI slices. |

<details>
<summary><b>Parameters</b> (2)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `n_layers` | enum | `3` | n_layers=3 → (0.3, 0.006, 0.3) S/m — brain/skull/skin (for EEG) n_layers=1 → (0.3,) S/m — inner skull only (for MEG-only) Options: `3`, `1`. |
| `ico` | string | `4` | ICO subdivision |

</details>

<!-- brainlife-app:end -->

## Running the App

### On brainlife.io

Open the App at [doi.org/10.25663/brainlife.app.899](https://doi.org/10.25663/brainlife.app.899), choose `neuro/freesurfer` from your project, set the parameters and press **Execute**. The results are saved to your project as `neuro/meg/fif`, `report/html`.

### Locally

You need [Singularity/Apptainer](https://apptainer.org/) (the App runs in `brainlifemeeg/mne-freesurfer:1.12.1-7.4.1`), [`jq`](https://jqlang.github.io/jq/), `FREESURFER_LICENSE`, `FS_LICENSE`.

1. Clone this repository.
2. Create `config.json` with the paths to your inputs and the parameters:

   ```json
   {
       "freesurfer": "/path/to/freesurfer/freesurfer",
       "n_layers": "3",
       "ico": "4"
   }
   ```

3. Run `./main`.

## Troubleshooting

- **Please set FREESURFER_LICENSE or FS_LICENSE in resource environment**: Configure a FreeSurfer license on the compute resource.

## Configuration Parameters

| key | type | default | description |
|---|---|---|---|
| `n_layers` | int (`3` or `1`) | `3` | Number of BEM layers / conductivity model passed to `mne.make_bem_model()`: `3` for brain/skull/skin conductivities `(0.3, 0.006, 0.3)` S/m (EEG or combined MEG+EEG), `1` for inner-skull-only conductivity `(0.3,)` S/m (MEG-only). |
| `ico` | int \| null | `4` | Icosahedron subdivision order for the BEM surfaces (e.g. `4` → 2562 vertices, `5` → 10242 vertices). If omitted/`null`, no subsampling is applied. |
| `subjects_dir` | string \| null | `null` | Optional override for the FreeSurfer `SUBJECTS_DIR`. If omitted, it is derived from the parent directory of the `freesurfer` input path. |
| `subject` | string \| null | `null` | Optional override for the FreeSurfer subject name. If omitted, it is derived from the basename of the `freesurfer` input path. |

## Local Testing

```bash
# Update config.json with the path to your FreeSurfer subject directory
# Then run:
python main.py
```

## Technical Details

- Requires a FreeSurfer license: the `main` script reads `FS_LICENSE`/`FREESURFER_LICENSE` from
  the resource environment and writes it to `license.txt` before the container starts.
- Container image: `docker://brainlifemeeg/mne-freesurfer:1.12.1-7.4.1`.
- The watershed BEM step is skipped automatically when the required surfaces already exist in
  the subject's FreeSurfer directory.

## Citing

If you use this App, please cite it, the software it builds on and brainlife.io:

- Make BEM model. brainlife.io App. [doi:10.25663/brainlife.app.899](https://doi.org/10.25663/brainlife.app.899)
- Gramfort, A. et al. MEG and EEG data analysis with MNE-Python. Front. Neurosci. 7, 267 (2013). https://doi.org/10.3389/fnins.2013.00267
- Fischl. FreeSurfer. *NeuroImage* 62:774-781 (2012). [doi:10.1016/j.neuroimage.2012.01.021](https://doi.org/10.1016/j.neuroimage.2012.01.021)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. *Scientific Data* 6:69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)

## Authors

- Maximilien Chaumon (https://github.com/dnacombo)
- obVdo (https://github.com/obVdo)

## Funding

[![Wellcome-226486/Z/22/Z](https://img.shields.io/badge/Wellcome-226486%2FZ%2F22%2FZ-purple.svg)](https://wellcome.org/)
[![NIH-NINDS-U24NS140384](https://img.shields.io/badge/NIH_NINDS-U24NS140384-green.svg)](https://reporter.nih.gov/project-details/11261037)
[![NIH-NINDS-UM1NS132207](https://img.shields.io/badge/NIH_NINDS-UM1NS132207-green.svg)](https://reporter.nih.gov/project-details/11411328)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://reporter.nih.gov/project-details/10262925)
[![NIH-NIBIB-R01EB030896](https://img.shields.io/badge/NIH_NIBIB-R01EB030896-green.svg)](https://reporter.nih.gov/project-details/10428625)
[![NIH-NIMH-R01MH126699](https://img.shields.io/badge/NIH_NIMH-R01MH126699-green.svg)](https://reporter.nih.gov/project-details/10460628)
[![NIH-NIDCD-R01DC020088](https://img.shields.io/badge/NIH_NIDCD-R01DC020088-green.svg)](https://reporter.nih.gov/project-details/11140284)
[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-2203524](https://img.shields.io/badge/NSF_BCS-2203524-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2203524)
[![NSF-IIS-1636893](https://img.shields.io/badge/NSF_IIS-1636893-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-OAC-2148729](https://img.shields.io/badge/NSF_OAC-2148729-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148729)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NSF-IIS-2148700](https://img.shields.io/badge/NSF_IIS-2148700-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148700)
[![NSF-OAC-1916518](https://img.shields.io/badge/NSF_OAC-1916518-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1916518)

<sub>brainlife.io, which runs this App, is publicly funded by Wellcome, the U.S. National Institutes of Health and the U.S. National Science Foundation; acknowledging that support helps keep it running.</sub>

## License

See [LICENSE](LICENSE).
