# ELLA synovial fluid time series

Lectin ELLA (enzyme-linked lectin assay) analysis of equine synovial fluid sampled over time, comparing a Synovitis joint with a Saline (control) joint in the same horse. There is one 96-well plate per horse per lectin.

The notebook fits a 4PL standard curve for each plate and converts sample ODs to µg/mL. It then averages the duplicate wells, flags pairs with a high CV, plots each lectin, and fits a mixed model (Synovitis vs Saline at each time point).

## Run it

1. Open `ELLA_SF_timeseries.ipynb` in Google Colab (or Jupyter).
2. Fill in the **SETUP** cell: the top standard, the dilution step and the sample dilution for each lectin, plus `LAYOUT_FILE`.
3. Check the **MISSING SAMPLES** cell.
4. Upload the input files (tick `UPLOAD_FILES` in Colab), then **Runtime → Run all**.

To run it locally instead:

```bash
pip install -r requirements.txt
jupyter notebook ELLA_SF_timeseries.ipynb
```

Put the input files in the same folder as the notebook.

## Input files (not included in this repository)

| File | Contents |
|---|---|
| Layout workbook (`.xlsx`, name set in `LAYOUT_FILE`) | One sheet per horse, named like `Gryphon (194)`. The plate grid starts on the row whose first cell is `A`, and plate columns 1-12 are Excel columns B-M. |
| `<lectin>_<horse>.csv` | One plate-reader export per lectin per horse, e.g. `SNA_Gryphon.csv` or `sna_194.csv`. Each file holds one OD block (the reader has already subtracted 650 nm). |

**Plate layout:** Rows A-B hold the standard curve in duplicate (column 1 is the DPBS blank, columns 2-12 are STD 1-11). Rows C-D hold LF, rows E-F hold RF, and columns 1-11 run from 0 h to 840 h. The column 12 SF controls are ignored.

## Outputs

| File | Contents |
|---|---|
| `wells_concentrations.csv` | Every sample well: OD, curve status, µg/mL |
| `per_sample_concentrations.csv` | Duplicate mean, CV and log2 for each sample |
| `high_cv_samples.csv` | Samples where the duplicate CV is above `CV_LIMIT` |
| `sample_counts.csv` | n per group per time point |
| `violinplot_<lectin>.svg`, `timecourse_<lectin>.svg` | Figures |
| `model_results_S_vs_C.csv` | Synovitis/Saline fold change, 95% CI, raw and BH-adjusted p |

## Model

`log2_conc ~ Tx * C(time_h) + (1 | Horse) + (1 | Joint within Horse)`. If the joint random effect cannot be fitted, the model falls back to horse only. P-values are BH-corrected across time points within each lectin.

## Requirements

Python 3 with the packages in `requirements.txt`.

## License

Not yet chosen.
