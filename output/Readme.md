# Experimental Outputs

This folder contains selected result summaries exported from the experiments in the `notebooks/` directory. The files include model evaluation metrics, validation and hyperparameter summaries, spatial encoder results, graph-sensitivity results, rainfall event-based metrics, and end-to-end fine-tuning comparisons.

## File Guide

| File | Description |
|---|---|
| `gru_test_results.txt` | GRU test metrics (MAE, RMSE, and R²) for each random seed and target variable. |
| `gru_test_summary.txt` | Mean and standard deviation of GRU test metrics across seeds. |
| `gru_validation_summary.txt` | GRU validation-loss, selected-epoch, and training-time summaries for the tested hidden sizes and epoch limits. |
| `lstm_test_results_by_seed.txt` | LSTM test metrics and selected validation checkpoints for each seed. |
| `lstm_test_summary.txt` | Mean and standard deviation of LSTM test metrics across seeds. |
| `spatial_encoder_training_summary.txt` | Training reconstruction losses, epoch information, training time, and checkpoint paths for GCN, GAT, and GATv2. |
| `spatial_encoder_reconstruction_by_seed.txt` | Per-seed reconstruction MSE results for the three spatial encoders, including variation across training years. |
| `spatial_encoder_reconstruction_summary.txt` | Aggregated reconstruction MSE comparison for GCN, GAT, and GATv2. |
| `spatial_knn_sensitivity_results.txt` | Per-seed reconstruction results and training times for alternative graph neighborhood sizes (k = 4, 8, and 12). |
| `spatial_knn_sensitivity_summary.txt` | Mean and standard deviation of the k-NN sensitivity results across seeds. |
| `gatv2_gru_tuning_individual.txt` | Per-seed validation loss, best epoch, and training time for the tested GATv2-GRU hidden dimensions. |
| `gatv2_gru_tuning_summary.txt` | Aggregated validation loss, best epoch, and training-time summaries for GATv2-GRU tuning. |
| `csi_hss_results_by_seed.txt` | Per-seed Critical Success Index (CSI) and Heidke Skill Score (HSS), including hits, misses, false alarms, and correct negatives. |
| `csi_hss_summary.txt` | Mean and standard deviation of CSI and HSS across seeds, models, years, and rainfall thresholds. |
| `end_to_end_experiment_summary.csv` | Per-seed comparison of frozen and end-to-end validation MSE, validation improvement, best epoch, training time, and checkpoint path. |
| `test_metrics_by_seed.csv` | Per-seed test metrics for the frozen and end-to-end GATv2-GRU models, reported separately for 2024, 2025, and the pooled test period. |
| `test_metrics_mean_std.csv` | Mean and standard deviation of the frozen and end-to-end test metrics across seeds, for each test year and the pooled period. |
| `figure_generation_metric_check_by_seed.csv` | Per-seed metric checks for the raw GRU and GATv2-GRU results used in figure generation. |
| `figure_generation_metric_check_summary.csv` | Mean and standard deviation of the figure-generation metric checks for raw GRU and GATv2-GRU. |

## Experimental Setup

- **Forecast period:** Annual one-year-ahead forecasting.
- **Test years:** 2024 and 2025; pooled results combine both years.
- **Random seeds:** 42, 123, and 2023, where applicable.
- **Forecast variables:** Rainfall and maximum temperature.
- **Rainfall event thresholds:** 2.5 mm/day and 5.0 mm/day for CSI and HSS calculations.
- **Spatial graph sensitivity:** k-NN neighborhood sizes of 4, 8, and 12.

## Notes on Interpretation

- Files ending in `by_seed` or `individual` contain seed-level or run-level results. Files labelled `summary` contain aggregated statistics.
- `test_metrics_by_seed.csv` and `test_metrics_mean_std.csv` document the frozen-versus-end-to-end experiment. They should be read separately from the figure-generation metric-check files.
- The figure-generation metric-check files are a separate set of exported GRU and GATv2-GRU metrics. They are retained as checks for the corresponding figures and should not be treated as interchangeable with the frozen-versus-end-to-end test results.
- Checkpoint paths recorded in some files (for example, `/kaggle/working/`) refer to the original Kaggle runtime. The checkpoint files themselves are not included in this output folder.
- These files are selected experimental summaries; they do not replace the notebooks, model-ready datasets, or full execution environment needed to reproduce the experiments.

For notebook descriptions, data requirements, and the recommended execution sequence, see the [notebooks README](../notebooks/README.md).
