# Make BEM Model

[![Run on Brainlife.io](https://img.shields.io/badge/Brainlife-bl.app.899-blue.svg)](https://doi.org/10.25663/brainlife.app.899)

## Description

This Brainlife.io application computes a Boundary Element Model (BEM) conductor model from a
FreeSurfer `recon-all` output, for use in forward/inverse modelling of MEG/EEG source estimates.
If the BEM surfaces (`inner_skull.surf`, `outer_skull.surf`, `outer_skin.surf`) are not already
present in the subject's FreeSurfer `bem/` directory, the app first extracts them by running
FreeSurfer's watershed algorithm (`mne watershed_bem`). It then builds the BEM model with
`mne.make_bem_model()` and computes the conductor model solution with `mne.make_bem_solution()`.
A QC figure is rendered with `mne.viz.plot_bem()` and an interactive slice-by-slice report is
built with `mne.Report.add_bem()`.

The app generates:
- BEM conductor model solution for forward modelling
- QC thumbnail comparing the coronal, axial and sagittal middle slices with the BEM surfaces overlaid
- Interactive HTML report with a slider through the MRI slices and BEM contours

## Inputs

- **`freesurfer`** (`neuro/freesurfer`): FreeSurfer subject directory from `recon-all` (required).
  Also accepted under the config key `output` as a fallback alias for the same path.

## Outputs

- **`out_dir/meg.fif`** (`neuro/meg/fif`, tag `bem`): BEM conductor model solution for forward modelling
- **`out_figs/bem_thumb.png`** (`generic/image/png`): QC thumbnail of the BEM surfaces (coronal/axial/sagittal middle slices)
- **`out_dir_report/report.html`** (`report/html`): interactive HTML report with BEM contours across MRI slices

## Configuration Parameters

| key | type | default | description |
|---|---|---|---|
| `n_layers` | int (`3` or `1`) | `3` | Number of BEM layers / conductivity model passed to `mne.make_bem_model()`: `3` for brain/skull/skin conductivities `(0.3, 0.006, 0.3)` S/m (EEG or combined MEG+EEG), `1` for inner-skull-only conductivity `(0.3,)` S/m (MEG-only). |
| `ico` | int \| null | `4` | Icosahedron subdivision order for the BEM surfaces (e.g. `4` → 2562 vertices, `5` → 10242 vertices). If omitted/`null`, no subsampling is applied. |
| `subjects_dir` | string \| null | `null` | Optional override for the FreeSurfer `SUBJECTS_DIR`. If omitted, it is derived from the parent directory of the `freesurfer` input path. |
| `subject` | string \| null | `null` | Optional override for the FreeSurfer subject name. If omitted, it is derived from the basename of the `freesurfer` input path. |

## Usage

### Running on Brainlife.io

1. Upload or select a FreeSurfer `recon-all` output directory for the subject (datatype `neuro/freesurfer`).
2. Select the "Make BEM Model" app.
3. Optionally configure `n_layers` and `ico`.
4. Submit the task.
5. Review the BEM QC thumbnail and the interactive report in the task output viewer.

### Local Testing

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

## Authors

- Maximilien Chaumon (https://github.com/dnacombo)
- obVdo (https://github.com/obVdo)

## Citations

- Hayashi, S., Caron, B.A., Heinsfeld, A.S. et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. Nat Methods 21, 809–813 (2024). https://doi.org/10.1038/s41592-024-02237-2
- Gramfort, A. et al. MEG and EEG data analysis with MNE-Python. Front. Neurosci. 7, 267 (2013). https://doi.org/10.3389/fnins.2013.00267

## Funding Acknowledgement

brainlife.io is publicly funded. We kindly ask that you acknowledge the funding below in your code and publications.

[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-1636893](https://img.shields.io/badge/NSF_BCS-1636893-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-ACI-1916518](https://img.shields.io/badge/NSF_ACI-1916518-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1916518)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://grantome.com/grant/NIH/R01-EB029272-01)
[![NIH-NIBIB-R01EB030896](https://img.shields.io/badge/NIH_NIBIB-R01EB030896-green.svg)](https://grantome.com/grant/NIH/R01-EB030896-01)

## License

Copyright (c) 2026 MEEG Brainlife team. Licensed under AGPL-3.0, see [license.txt](license.txt).
