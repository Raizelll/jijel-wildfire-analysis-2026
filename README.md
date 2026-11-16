# Mapping Forest Loss Following the August 2026 Wildfires in Jijel, Algeria

Code accompanying the preprint *"Mapping Forest Loss Following the August 2026 Wildfires in Jijel, Algeria"* (Raid Zella, 2026).

The workflow compares Sentinel-2 composites from before and after the fires, applies a U-Net segmentation model to map forest cover on both dates, and obtains forest loss as the difference between the two masks. NDVI and NBR changes are used as independent supporting evidence.

<!-- TODO: add a preview image, for example: ![Forest loss map](figures/your_main_map.png) -->

## Main results

| Quantity | Value |
|---|---|
| Area analysed (valid on both dates) | 2,384.4 km² |
| Pre-fire forest (U-Net) | 943.7 km² |
| Forest lost | 468.4 km² (46,840 ha), 49.6% of pre-fire forest |
| Mean ΔNDVI, loss pixels vs. stable forest | -0.413 vs. -0.013 |
| Mean ΔNBR, loss pixels vs. stable forest | -0.631 vs. -0.016 |
| Pre-fire map vs. ESA WorldCover 2021 | Precision 0.917, recall 0.603 |

See the paper for the full analysis and its limitations.

## Repository structure

```
.
├── README.md
├── LICENSE
├── requirements.txt
├── notebooks/
│   ├── 01_study_area_and_data_preparation.ipynb
│   └── 02_model_inference.ipynb
└── figures/            maps and figures used in the paper
```

## Workflow

Run the notebooks in order.

1. **`01_study_area_and_data_preparation.ipynb`**
   Defines the study area (Jijel wilaya, FAO GAUL 2015) and builds the Sentinel-2 median composites with Google Earth Engine:
   - pre-fire: 12 to 22 August 2026
   - post-fire: 1 to 10 September 2026
   - cloud cover below 5% (`COPERNICUS/S2_SR_HARMONIZED`)

   It computes NDVI and NBR, adds the ESA WorldCover 2021 tree-cover mask as a reference band, reports spectral change statistics, and exports the two GeoTIFFs (bands: B2, B3, B4, B8, B11, B12, NDVI, NBR, forest mask) to Google Drive.

2. **`02_model_inference.ipynb`**
   Loads the exported GeoTIFFs and applies the U-Net with sliding-window inference. Forest loss is defined as pixels that are forest before the fires and not after. The notebook then computes areas, runs the NDVI/NBR evidence analysis, compares the pre-fire map with WorldCover 2021, saves the output rasters and `results.json`, and draws the maps.

## Model

| Setting | Value |
|---|---|
| Architecture | U-Net (`segmentation_models_pytorch`), ResNet34 encoder |
| Input bands | B2, B3, B4 (surface reflectance, 0 to 1) |
| Patch size / stride | 256 / 128 pixels |
| Decision threshold | 0.8 |

The model was trained mainly on Sentinel-2 data from El Tarf (near Jijel) with ESA WorldCover labels, supplemented by additional private data. It is applied to Jijel without retraining.

**Model weights and training data:** the weights file and the private training data are not included in this repository. `MODEL_PATH` in the second notebook must point to your own trained weights file.

## Data

- **Sentinel-2 L2A (harmonized):** Copernicus, accessed through Google Earth Engine.
- **ESA WorldCover v200 (2021):** used only as a reference, not as ground truth for 2026.
- **Administrative boundaries:** FAO GAUL 2015.
- **Training data:** partly private, not distributed.

## Citation

If you use this work, please cite:

```
Zella, R. (2026). Mapping Forest Loss Following the August 2026 Wildfires
in Jijel, Algeria. EarthArXiv preprint (not peer reviewed).
```

## Author

Raid Zella, Independent Researcher, Algeria.
GitHub: [RaiZelll](https://github.com/RaiZelll)

## License

Code is released under the license in the `LICENSE` file. Figures are shared under CC BY 4.0.
