# Pill defect detection

We are trying to find defects in photos of manufactured pills, cracks, scratches, stains etc using the pill category of the [MVTec AD dataset](https://www.mvtec.com/research-teaching/datasets/mvtec-ad). Two models, patch and whole-image learn only from pictures of normal pills, then flag a pill as defective when a new photo cannot be rebuilt accurately. One model looks at the whole pill. The other looks at small overlapping patches of the same photo. We then compare which approach is better at spotting the defect and showing where it is. 

## Research question

Does examining small image patches improve the detection and localization of subtle pill defects compared with examining the whole image?

The planned comparison has two convolutional autoencoders. Both learn only from normal images. Reconstruction differences will produce anomaly scores and defect heatmaps.

1. Whole RGB images resized to 256 × 256.
2. Overlapping 128 × 128 patches taken from the original images.

The official test set stays separate from training and validation. Test labels and defect masks are reserved for evaluation. Both approaches are planned to use the same scoring procedure and the same metrics.

## Dataset

Download the pill category from the MVTec AD site and keep the original subfolders and filenames:

https://www.mvtec.com/research-teaching/datasets/mvtec-ad

Place the files locally at:

```text
data/pill/train/good/
data/pill/test/
data/pill/ground_truth/
```

`split.csv` currently contains only the header `relative_path,split`. `notebooks/01_data_setup.ipynb` will add rows after the downloaded training images have been checked. Each path is relative to `data/pill/` and will point at an original file under `train/good/`. The split label will be `train` or `val`. This repository does not invent filenames or assignments.


- 267 normal training images
- 26 normal test images
- 141 defective test images
- Original size 800 × 800 pixels
- Seven defect groups: color, combined, contamination, crack, faulty_imprint, pill_type, and scratch
- Ground-truth masks mark defective pixels

The planned split of the normal training images is about 214 training images and 53 validation images. Split the original images first. Create patches and any augmented copies only after that split exists. If the on-disk training count is not 267, stop and agree on the split before writing `split.csv`.


### Plan

- Dataset checks against the downloaded files
- The shared train/validation assignment
- Either autoencoder, training, checkpoints, or training curves
- Patch extraction, background-patch handling, and heatmap reconstruction
- Threshold selection, test evaluation, defect-type comparison, localization, runtime comparison, and poster figures


## Environment

Use Python 3.10 or newer

Windows (PowerShell), from this project folder:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python -m ipykernel install --user --name pill-defect --display-name "Python (pill-defect)"
```

macOS or Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python -m ipykernel install --user --name pill-defect --display-name "Python (pill-defect)"
```

## Notebook order

1. `notebooks/01_data_setup.ipynb` — check the download, then write the shared split.
2. `notebooks/02_whole_image.ipynb` — whole-image autoencoder, after `split.csv` has real rows.
3. `notebooks/03_patch_model.ipynb` — patch autoencoder, using the same split. This can proceed alongside notebook 02 once the split exists.
4. `notebooks/04_evaluation.ipynb` — thresholds, test metrics, and poster figures after both models have saved checkpoints.

## What stays off GitHub

Dataset images, ground-truth masks, checkpoints, and figures are local. `.gitignore` excludes `data/` and `outputs/`, along with virtual environments and notebook checkpoints. Notebooks, `requirements.txt`, `README.md`, and `split.csv` are the files intended for the repository. `split.csv` stores paths and split labels, not image bytes.

## Responsibilities

| Member | Responsibilities |
| --- | --- |
| MC | Dataset checks, shared split, whole-image model (01_data_setup.ipynb, 02_whole_image.ipynb) |
| MK | Patch extraction, patch model, heatmap reconstruction (03_patch_model.ipynb) |
| TS | Shared evaluation, comparison charts, poster figures (04_evalution.ipynb) |
