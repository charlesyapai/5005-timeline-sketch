# SkinProfile roadmap

The diagram: **[roadmap.svg](roadmap.svg)**, read left to right. It opens in any browser and imports into PowerPoint with Insert > Pictures (right-click > Convert to Shape to edit the text).

| Step | Status | What | Side notes |
|---|---|---|---|
| APPA-REAL |  | 7,591 img · apparent age |  |
| UTKFace |  | 23,708 img · real age |  |
| ACNE04 (+ v2) |  | 1,457 img · acne severity, Hayashi 0–3 |  |
| MegaAge-Asian |  | 43,945 img · Asian age coverage |  |
| Fitzpatrick17k + SkinCon |  | 16,577 img + 3,230 concept rows · clinical atlas |  |
| FairFace |  | 97,698 img · eval slices only |  |
| SCIN |  | 10,379 img · 5,033 cases · phone photos, anchor set |  |
| DDI | Pending | 656 img · fairness audit only | all under data/raw/ · gitignored · never leaves the Mac |
| S1  Data acquisition | Done | Get every dataset on disk with verified counts and licences. | Mac · ≈1 day · done 19 Oct |
| S2  Data understanding & QC | Done | Know what the images and labels really are before modelling. | 201k images checked · 0 decode errors |
| S3  Feature EDA | Done | Find which measurable image properties relate to each label, and how datasets differ. | Mac CPU · ~1 day features computed on the skin mask only |
| S4  Classical ML | Done | The baseline every later model must beat. | Mac CPU, 4 threads · 1–2 days, minutes of compute |
| S5  From-scratch CNN | Done | Build and train our own multi-task CNN, to understand every part. | Mac MPS · 10–19 min per run · done 20 Oct |
| S6  Open-weight transfer | DONE · ablation skipped | How much does pretraining add, and which kind helps most? | ~3 days |
| HF GPU? |  | only if sweeps overrun |  |
| S10  Face structure & ageing | Done | Folds, sagging and jaw alignment from combined open-source face models, as a tab in the app. | MPS GPU · inference only weights in models/third_party/ |
| SCIN head/neck test set |  | 1,575 img · 737 cases · phone photos |  |
| S9  Acne lesion detection | Done | Boxes around acne lesions and a count, as an optional layer in the app. | MPS GPU · about 1.3 GPU-hours in all v1 boxes only · clinic photos, no phone-photo boxes |
| S7  Comparison & analysis | DONE (v2) | One honest table, and the story behind it. | Mac CPU · ~2 days tone labels slice results only, never a model input |
| S11  Hair: shape, season, styles | Done | Rule-based hairstyle and hair-colour advice from face shape and a colour season, with a non-generative preview on the person's own frame, as tab 7 of the app. | nothing generative: rules + compositing Charles's checkpoint answers: decisions.md 2026-10-22 |
| S8  Product composition | v2 UI · guided sweep · view routing · focus views | A small custom app the group can restyle: FastAPI backend, plain web frontend. | ~2 days · run locally: uvicorn, app in stages/S8_product/app/ |
| later hosting (optional) |  | HF Space (Docker SDK, CPU); or any small VPS; weights pulled from a private HF; model repo via a token secret |  |
| Inspect |  | docs/CATALOGUE.md (master index); docs/experiments.csv (every run); docs/decisions.md (dated log); stages/<S>/README.md |  |

## Where to look

Paths are relative to the repo root. Notebooks sit in each stage's `notebooks/` folder. Master index: [CATALOGUE.md](CATALOGUE.md); every run: [experiments.csv](experiments.csv).

| Stage | Status | README | Notebooks | Outputs | Headline result |
|---|---|---|---|---|---|
| S1 Data acquisition | DONE | `stages/S1_data_acquisition/README.md` | — | `docs/data_inventory.md`; `data/raw/<dataset>/ (gitignored)` | 7 of 8 datasets on disk (DDI pending); 201,355 images, 0 decode errors |
| S2 Data understanding & QC | DONE | `stages/S2_data_understanding_qc/README.md` | — | `stages/S2_data_understanding_qc/outputs/`; `configs/splits_summary.csv` | SCIN split 7,043 / 1,761 / 1,575; 1,085 duplicate exclusions |
| S3 Feature EDA | DONE | `stages/S3_feature_eda/README.md` | `01_datasets_and_labels, 02_features_by_label_and_dataset, 03_structure_pca_umap_tone` | `stages/S3_feature_eda/outputs/ (single_feature_auroc.csv, dataset_probe_features.csv)` | "which dataset" probe: hand-crafted 0.877 vs DINOv2 0.949 (chance 0.143) |
| S4 Classical ML | DONE | `stages/S4_classical_ml/README.md` | `01_baselines_and_cv, 02_importance_and_leakage` | `stages/S4_classical_ml/outputs/ (summary.csv)` | SCIN erythema AUROC 0.59; acne QWK 0.67; age MAE 11.5 y |
| S5 From-scratch CNN | DONE | `stages/S5_scratch_cnn/README.md` | `01_architecture_and_training, 02_results_and_gradcam` | `stages/S5_scratch_cnn/outputs/ (runs/<run>/, runs_test_main_metrics.csv)` | acne QWK 0.83; age MAE 8.3 y; SCIN signs AUROC 0.49–0.57 (about chance) |
| S6 Open-weight transfer | DONE · ablation skipped | `stages/S6_open_weight_transfer/README.md` | `01_embeddings_umap, 02_backbone_comparison` | `stages/S6_open_weight_transfer/outputs/ (probes_all_backbones.csv, runs/{openclip,siglip2,monet}/)`; `models/final/<run>/ (release models-v1)` | LoRA val composite MONET 0.832, SigLIP 2 0.817, OpenCLIP 0.815; MONET LoRA test erythema 0.70, refer 0.70, age MAE 2.81 y; ablation skipped (heat) |
| S7 Comparison & analysis | DONE (v2) | `stages/S7_comparison_analysis/README.md` | `02_comparison_v2 (final), 01_comparison_v1 (pre-LoRA)` | `stages/S7_comparison_analysis/outputs/ (master_table.md, winners.csv, calibration_app_model.csv)` | MONET LoRA val pick for 7/8 targets (erythema 0.70, refer 0.70, age 2.81 y); clinic → phone drop ≈ −0.2; Platt on SCIN val: refer ECE 0.087 → 0.044 |
| S8 Product composition | v2 UI · guided sweep · view routing · focus views | `stages/S8_product/README.md` | — (run stages/S8_product/run_local.sh) | `stages/S8_product/app/, bundles/monet/ (thresholds.yaml, model card)`; `outputs/{eyes,structure}/`; `models/final/monet/` | MONET LoRA + Platt thresholds served; eight-tab page with S9 lesion boxes, eyes and S10 structure & ageing; guided head-sweep capture, view routing and per-tab focus views; 7 open-source models per scan; 610 tests |
| S9 Acne lesion detection | DONE | `stages/S9_lesion_detection/README.md` | `01_lesion_detector` | `stages/S9_lesion_detection/outputs/runs/ (test_metrics.json)`; `models/final/lesion_frcnn_v1/ (release models-v1)` | test AP@0.5 0.323; count MAE 5.6 vs v1 boxes; grade QWK 0.818 (MONET 0.853, OpenCLIP 0.821); small lesions and severe acne weak |
| S10 Face structure & ageing | DONE | `stages/S10_face_structure/README.md` | — (run stages/S8_product/run_local.sh; tab 6) | `stages/S10_face_structure/outputs/ (thirdparty_latency.csv, thirdparty_smoke_summary.json)`; `stages/S8_product/outputs/structure/` | 4 face models + 19 measures live in the app: 2.43 s per 3-frame request on MPS, 10.8 s CPU; 3DDFA_V2 jaw ρ 0.90 but values compressed 3–4× |
| S11 Hair | DONE | `stages/S11_hair/README.md` | `stages/S11_hair/notebooks/01_faceshape_and_season_checks.ipynb` | `stages/S11_hair/outputs/ (season_appa.csv, faceshape_appa.csv, …)`; `stages/S11_hair/assets/` | Face shape (5 ratios → 7 shapes), colour season (4) and 28 scored styles live as tab 7, with a Lab recolour and an SVG cut on the person's frame; rules only, nothing generative |
