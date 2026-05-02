# MaVi-Volume

MaVi-Volume is a machine-vision workflow for estimating fluid volume in head-fixed mouse consumption experiments from grayscale tube images.

This repository contains:
- A trained ResNet-50 checkpoint for volume prediction.
- A notebook for model evaluation/inference.
- An older notebook that includes full training and checkpoint export.

## Repository Structure

- `model_run.ipynb`: Main notebook to load a trained model, evaluate on the dataset, and export predictions.
- `training.ipynb`: Full training pipeline (data loading, preprocessing, model training, evaluation, and save).
- `models/`: Saved model checkpoints (`.pth`).
- `VolPicturesRound1/`: Image data folders (`Attempt1` ... `Attempt10`) and Excel metadata file.
- `testing results/`: Output folder for inference/evaluation exports.
- `results/`: Output folder used by the training notebook.

## Data Requirements

The notebooks expect this dataset layout under `VolPicturesRound1/`:

1. Excel file:
	 - `HFB Volume Image Gathering.xlsx`
	 - Required sheets:
		 - `# of Droplets`
		 - `Initial Tube Weights`
		 - `Final Tube Weights`
2. Image folders:
	 - `Attempt1` to `Attempt10`
	 - Image naming pattern per folder:
		 - `Run{run}Rig1Tube{tube}final.bmp`

Volume labels are computed as:
- `mass = Final Tube Weight - Initial Tube Weight`
- `volume = mass / p`

By default, `p = 1.0` g/mL in the notebooks.

## Environment Setup (Windows PowerShell)

1. Create and activate a virtual environment:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

2. Install dependencies:

```powershell
pip install -r requirements.txt
```

3. Launch Jupyter:

```powershell
jupyter notebook
```

Open `model_run.ipynb` or `training.ipynb`.

## How To Use

### Option A: Run Inference / Evaluation (recommended)

Use `model_run.ipynb` to evaluate a trained model and export predictions.

1. Open `model_run.ipynb`.
2. Update `data_dir` so it points to your local `VolPicturesRound1` folder.
3. Verify checkpoint path in the model loading cell, for example:
	 - `models/volume_predictor_resnet50_2026-05-01_13-17-08.pth`
4. Run all cells in order.
5. Find outputs in:
	 - `testing results/test_predictions.xlsx`
	 - prediction vs true-value scatter plot in notebook output.

### Option B: Retrain The Model

Use `training.ipynb` to train from scratch and save a new checkpoint.

1. Open `training.ipynb`.
2. Update `data_dir` to your local dataset path.
3. Run cells in order (preprocessing, split, training loop, evaluation).
4. Saved checkpoint appears in `models/` with a timestamped filename.
5. Training exports are written to `results/`.

## Notes

- The notebooks currently use hard-coded Windows paths. If you move this repo, update `data_dir` accordingly.
- If some image files are missing, the loader will skip them and print warnings.
- CUDA is used automatically when available; otherwise the model runs on CPU.

## Troubleshooting

- `FileNotFoundError` for images or Excel:
	- Check `data_dir` and dataset naming conventions.
- `ModuleNotFoundError`:
	- Re-activate `.venv` and reinstall with `pip install -r requirements.txt`.
- Slow runtime:
	- Confirm PyTorch is using GPU (`cuda`) in the notebook device cell.

