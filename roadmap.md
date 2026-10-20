# SkinProfile roadmap

Status as of **20 Oct 2026**. The plan is one long flowchart, read left to right: **[roadmap.svg](roadmap.svg)**.
It opens in any browser and imports into PowerPoint with Insert > Pictures; the text stays editable. Each stage box
is followed by its individual steps (small boxes). Plain-text notes say where each step runs, how long it takes and
where its files go.

The flow: datasets (source, licence, labels used, known issues) → S1 → S2 → S3, then three modelling branches
(S4 classical, S5 from-scratch CNN, S6 open-weight transfer). All three converge on the held-out SCIN head/neck test
set (1,575 images, 737 cases, never trained on, each model scored once). From there it runs to S7 and on to the S8
custom app.

| Stage | Status | Objective | Individual steps | Out |
|---|---|---|---|---|
| S1 Data acquisition | Done | Get every dataset on disk with verified counts and licences | `scripts/download/<name>.py` → manifests + md5/sha256 → `verify_counts.py` → licence table | `docs/data_inventory.md` |
| S2 Data understanding & QC | Done | Know what the images and labels really are before modelling | `run_qc.py` → `find_duplicates.py` (phash ≤ 4, thumbnail corr ≥ 0.9) → `build_labels.py` → `make_splits.py` (1,085 duplicate exclusions) → `run_preprocess.py` (478 landmarks → crop → skin mask → Shades-of-Gray p=6 → 224 px raw + sog) | `outputs/qc/`, `labels.parquet`, `splits.json`, `images_224/` |
| S3 Feature EDA | Next | Find which measurable image properties relate to each label, and how datasets differ | colour (Lab), texture (LBP, GLCM, Laplacian), landmark geometry, metadata → `features.parquet` → distributions, correlation heatmap, PCA + UMAP, tone distribution | `outputs/eda/` |
| S4 Classical ML | Next | The baseline every later model must beat | standardise → one sklearn Pipeline per target → select on val (LR, RF, HistGB, kNN) → class weights → calibration → score once on test → permutation importance → learning curves | `outputs/classical/` |
| S5 From-scratch CNN | Planned | Build and train our own multi-task CNN, to understand every part | 4-stage CNN (32→256) + 5 heads → mixed-source sampler with label masks → CORAL / BCE / 101-bin CE losses → AdamW + cosine LR → augmentation ablation → early stopping → learning curves → Grad-CAM; 128 px first, then 224 | checkpoints in `models/` |
| S6 Open-weight transfer | Planned | How much does pretraining add, and which kind helps most? | score the matrix → load the six backbones (transformers / open_clip / timm / HF) → zero-shot (B, D) → frozen probes → source probe → LoRA on the top 2–3 (≈2.5 h per run, overnight) → winner per target on val | `models/final/` |
| S7 Comparison & analysis | Planned | One honest table, and the story behind it | predictions per model → one metrics script → temperature scaling on val → bootstrap 1,000 by case → fairness slices, domain shift, Grad-CAM / attention rollout, multi-frame | `outputs/results/` |
| S8 Product composition | Planned | A small custom app the group can restyle | `app/web/` capture (getUserMedia, several frames) → POST /predict on `app/server.py` (FastAPI, model loaded once) → `skinscan.preprocess` → backbone + 5 heads → aggregate frames → rules engine (`rules.yaml`) → JSON profile → results page with confidence bars and a "not a diagnosis" banner. pytest + TestClient, Dockerfile, model card. Local first (uvicorn); later, optionally, an HF Space (Docker SDK, CPU) or a small VPS | live demo + architecture slide |
