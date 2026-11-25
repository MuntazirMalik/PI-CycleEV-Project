# PI-CycleEV: Physics-Informed Cycle-Consistent GAN for Unpaired Cross-Season EV Time-Series Synthesis

Machine Learning (Fall 2026), Department of Computer Science, BUITEMS. Instructor: Ms. Poma Panezai.

| Name | Student ID |
|---|---|
| Muhammad Muntazir | 66305 |
| Taskeen Fatima | 66757 |
| Arooj Fatima | 66703 |

## What this project does
1. **Baseline (E1):** reproduces the TS-p2pGAN of Jeng (2025, *Sensors* 25(3), 749), a paired conditional GAN that generates SOC, battery voltage, motor torque and longitudinal acceleration from ten conditioning signals.
2. **Novelty (E3):** PI-CycleEV, an unpaired 1D CycleGAN that translates driving time series between summer and winter, with physics-informed losses (SOC-current consistency, velocity-acceleration consistency, a window-level plausibility penalty).
3. **Controls and ablations:** E2 (standard CycleGAN, no physics), three ablations that remove one physics term each, and a downstream SOC-estimation test (E4-E7) where synthetic windows are added to the training set of an SOC estimator (3 seeds, both seasonal directions).

All results in the notebook and in the paper come from the runs saved in `results/` and `models/`. The baseline was trained for 50 epochs (the base paper used 800) and each CycleGAN run for 100 epochs, once, with seed 42. The E3 run was continued from its epoch-70 checkpoint and the `abl_no_range` run from its epoch-20 checkpoint after Colab sessions ended; only the generators were restored, so the discriminators and optimisers were re-initialised at that point.

## Dataset
Battery and Heating Data in Real Driving Cycles (IEEE DataPort): https://ieee-dataport.org/open-access/battery-and-heating-data-real-driving-cycles

Download the **measurement data** zip (not the simulation data) and place the trip CSVs (`TripA01.csv` ... `TripB38.csv`, 70 trips: 32 summer, 38 winter) in `PI_CycleEV_Project/dataset/`, or put the zip anywhere inside `PI_CycleEV_Project/` and Section 01 unpacks it. The dataset is not included in this submission.

## Repository / folder layout
```
PI_CycleEV_Project_final.ipynb   the single end-to-end notebook (12 sections)
requirements.txt
README.md
results/   metrics, histories, figures, JSON summaries (full run)
models/    final generator weights (not in the ZIP; Drive link below)
diagrams/  original draw.io diagrams (.drawio) with PNG previews
paper/     LaTeX source (IEEEtran) and figures of the paper (GitHub repository only)
slides/    presentation (PowerPoint) (GitHub repository only)
```
The paper and the slides are submitted separately; the implementation ZIP contains the notebook, README, requirements, `results/` and `diagrams/`.

## How to run
1. Open `PI_CycleEV_Project_final.ipynb` in Google Colab and choose a **GPU runtime**.
2. Mount Google Drive, and set `PROJECT_ROOT` in cell 01.1 to the folder that holds `dataset/`, `results/` and `models/`. This is the only path to edit.
3. In cell 01.3 choose the mode:
   - `TEST_MODE = True`: whole pipeline on a small subset with 2 training epochs, a quick check that every cell runs.
   - `TEST_MODE = False` (full train): the configuration used for the reported results.
4. In full mode, `RETRAIN_IN_FULL_MODE = False` (the default) loads the saved weights from `models/` and only evaluates. Set it to `True` to retrain every model from scratch (several hours on a GPU; the training cells then overwrite the files in `models/` and `results/`).
5. Run all cells from top to bottom (**Runtime > Run all**).

## Versions used for the reported results
Python 3.13 (Colab) | TensorFlow 2.21.0 | NumPy 2.4.6 | pandas 2.2.3 | scikit-learn 1.6.1 | SciPy 1.16.3 | tslearn 0.10.0. The notebook prints the exact versions in cell 01.3.

## Runtime
Baseline: about 44 minutes for 50 epochs on a Colab GPU. CycleGAN runs: 100 epochs each, trained across several Colab sessions with checkpoint resumes (see Training notes in Section 00 of the notebook), so there is no single wall-clock figure per run. Evaluation with saved weights: about 25 minutes for the whole notebook (`notebook_runtime_minutes` in `results/reproducibility_summary_full.json`).

## Experiments
| ID | Description |
|---|---|
| E1 | TS-p2pGAN baseline (paired) |
| E2 | Standard CycleGAN, all physics weights = 0 |
| E3 | PI-CycleEV (physics weights 0.4 / 0.03 / 5.0) |
| E4 | Downstream SOC estimator trained on real source-season windows only, tested on real target-season test windows |
| E5 | E4 plus TS-p2pGAN windows (real source-season inputs with predicted outputs) |
| E6 | E4 plus standard-CycleGAN windows |
| E7 | E4 plus PI-CycleEV windows |
| Reference | Estimator trained on real target-season training windows (upper bound, not a fair competitor) |
| Ablations | E3 without the SOC-current term, without the velocity-acceleration term, without the range term |

## Model files and size
`models/` holds 11 generator weight files (about 635 MB). GitHub rejects single files over 100 MB, so the baseline weights (`ts_p2pgan_G_full.weights.h5`, 244 MB) are not stored in the repository: download them from https://drive.google.com/drive/folders/1PsuBb1hGLoQ_3vi6eVbeEk3ICcj4yzOG?usp=sharing and place them in `models/`. The CycleGAN weights are 42 MB each.

## Declaration of AI use
An AI assistant (Claude, Anthropic) was used for coding help, debugging and analysing the saved results. All experiments were run by the authors on Google Colab, and every number, table and figure in the paper comes from the saved outputs of those runs. The authors reviewed the final text and take responsibility for its content.

## Repository
https://github.com/MuntazirMalik/PI-CycleEV-Project
