# Sea-Ice AI Classification

## Do all 21 spectral bands matter?

This project investigates binary sea-ice/lead classification from 21-band optical Earth-observation imagery. It compares three machine-learning approaches — **Random Forest (RF)**, a **Convolutional Neural Network (CNN)** and a **Vision Transformer (ViT)** — and then tests whether a smaller spectral subset can preserve classification performance while reducing computational and environmental cost.

## Research question

**Can sea ice and leads be classified accurately from 21-band satellite imagery using a computationally efficient machine-learning approach, and can spectral feature selection reduce model complexity without sacrificing classification performance?**

## Why this matters

Sea ice and leads affect exchanges of heat, moisture and energy between the ocean and atmosphere. Satellite Earth observation allows these environments to be monitored over areas that are difficult to observe continuously in situ. At the same time, using more spectral information and more complex AI models does not automatically produce a better classifier. This project therefore evaluates both predictive performance and computational efficiency.

## Workflow

1. Manually label sea-ice/lead regions using the IRIS interface.
2. Extract labelled **3 × 3 × 21** image patches.
3. Balance the binary classes and split into training/test data.
4. Train RF, CNN and ViT classifiers.
5. Evaluate accuracy, confusion matrices, precision, recall and F1 score.
6. Apply the trained models to a spatial rollout region.
7. Aggregate RF feature importance by spectral band.
8. Retrain RF using only the five highest-ranked bands.
9. Compare runtime, energy use and CodeCarbon-estimated emissions.

## Main model results

All three models were evaluated on the same held-out test set of **1,580 samples**.

| Model | Test accuracy | Macro F1 | Mean prediction time |
|---|---:|---:|---:|
| **Random Forest** | **93.42%** | **0.934** | **0.0828 s** |
| ViT | 93.35% | 0.934 | 1.3279 s |
| CNN | 78.48% | 0.785 | 0.3778 s |

Random Forest and ViT achieved almost identical test performance, differing by only 0.07 percentage points in accuracy. On this Colab setup, however, RF inference was substantially faster. The CNN performed considerably worse than both RF and ViT.

### Confusion matrices

- Random Forest: `[[734, 41], [63, 742]]`
- ViT: `[[723, 52], [53, 752]]`
- CNN: `[[621, 154], [186, 619]]`

Class labels are intentionally kept as **0** and **1** here rather than assigning physical names without re-verifying the mask-label convention.

## Spatial rollout

The three trained models were applied to the same independent rollout region and reconstructed into 2D binary classification maps. Similar global accuracy does not imply identical spatial predictions, so the rollout maps provide an additional qualitative comparison between methods.

The final rollout figures will be stored in `figures/`.

## Spectral-band importance

Random Forest feature importance was reshaped from the flattened **3 × 3 × 21** input and summed over the nine spatial positions to obtain one importance value per spectral band.

The five highest-ranked bands were:

| Rank | Band | Importance |
|---|---:|---:|
| 1 | **1** | **0.1918** |
| 2 | **2** | **0.1454** |
| 3 | **3** | **0.0751** |
| 4 | **4** | **0.0516** |
| 5 | **15** | **0.0474** |

These values are a model-based importance measure, not proof that the same wavelengths are universally the most physically important for sea-ice/lead discrimination.

## 21 bands vs top 5 bands

A reduced RF model was trained using only Bands **1, 2, 3, 4 and 15**.

Across five Random Forest seeds:

| Input | Mean accuracy | Standard deviation |
|---|---:|---:|
| 21 bands | 93.05% | 0.10% |
| Top 5 bands | **93.66%** | 0.26% |

Reducing the input from 21 to 5 bands therefore **did not reduce classification performance in this experiment**. The five-band model achieved slightly higher mean accuracy, although with somewhat greater variability. This should not be interpreted as proof that five bands are universally superior.

## Computational efficiency

Mean RF training time was:

| Input | Mean training time |
|---|---:|
| 21 bands | 14.206 s |
| Top 5 bands | 5.716 s |

This corresponds to a **59.8% reduction in measured training time** while retaining comparable predictive performance.

## Environmental-cost measurement

CodeCarbon was used to estimate energy consumption and associated CO2-equivalent emissions. Because individual RF fits were short, the main comparison measured **five complete training runs** for each spectral configuration.

| RF workload | Energy | Estimated emissions |
|---|---:|---:|
| 21-band, 5 training runs | 0.00044974 kWh | 0.00021173 kg CO2eq |
| 5-band, 5 training runs | 0.00018756 kWh | 0.00008830 kg CO2eq |

This produced a measured reduction of approximately **58.3% in both energy use and estimated emissions** for the reduced-band workload. Absolute values are very small and specific to the Colab environment; the relative comparison is therefore more informative than the absolute footprint.

A fuller environmental and societal discussion is provided in [`ASSESSMENT.md`](ASSESSMENT.md).

## Reproducibility

The final notebook will be placed in `notebooks/` and the generated figures in `figures/`.

### Expected data files

The course data are not redistributed in this repository. To reproduce the analysis, place the prepared arrays in a local project directory using the following filenames:

- `X_train_balanced.npy`
- `X_test_balanced.npy`
- `y_train_balanced.npy`
- `y_test_balanced.npy`
- `image2.npy` for the spatial rollout stage

The notebook uses NumPy, pandas, Matplotlib, scikit-learn, TensorFlow/Keras, joblib and CodeCarbon. See [`requirements.txt`](requirements.txt).

### Environment

The analysis was carried out in **Google Colab**, including GPU acceleration for neural-network training. Runtime measurements are hardware-dependent and should be interpreted as relative comparisons within this experiment.

## Repository structure

```text
Sea-Ice-AI-Classification/
├── README.md
├── ASSESSMENT.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   └── README.md
├── figures/
│   └── README.md
└── results/
    ├── model_metrics.csv
    ├── band_reduction_metrics.csv
    └── environmental_metrics.csv
```

## Limitations

- The experiments use a limited labelled region from one image, so performance cannot automatically be assumed to generalise to other dates, locations, illumination conditions or sensors.
- Training labels were created manually and therefore contain potential labelling uncertainty.
- Impurity-based Random Forest feature importance can be affected by correlated predictors.
- Five RF seeds provide a repeatability check, not a universal statistical claim of superiority.
- Timing and CodeCarbon values depend on the Colab hardware/runtime used.
- Rollout maps can be compared spatially, but without independent ground truth across the full rollout region, differences between maps cannot automatically be labelled as errors.

## Video presentation

**Video link:** to be added before submission.

## Project status

The modelling, reduced-band experiment and CodeCarbon comparison are complete. Final repository packaging, figures and the presentation video are being prepared for reassessment submission.
