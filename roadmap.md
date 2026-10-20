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
| S3  Feature EDA | Done | Find which measurable image properties relate to each label, and how datasets differ. | Mac CPU · ~1 day; features computed on the skin mask only |
| S4  Classical ML | Done | The baseline every later model must beat. | Mac CPU, 4 threads · 1–2 days, minutes of compute |
| S5  From-scratch CNN | Done | Build and train our own multi-task CNN, to understand every part. | Mac MPS · 10–19 min per run · done 20 Oct |
| S6  Open-weight transfer | Next | How much does pretraining add, and which kind helps most? | ~3 days; expected picks A  DINOv2-B · general B  SigLIP 2 / OpenCLIP · vision-language C  FaRL · face D  DermLIP · dermatology (verify licence) E  MiVOLO v2 · age F  ConvNeXt-T · ImageNet control |
| HF GPU? |  | only if sweeps overrun |  |
| SCIN head/neck test set |  | 1,575 img · 737 cases · phone photos |  |
| S7  Comparison & analysis | Planned | One honest table, and the story behind it. | Mac CPU · ~2 days; tone labels slice results only, never a model input |
| S8  Product composition | Planned | A small custom app the group can restyle: FastAPI backend, plain web frontend. | ~2 days · run locally: uvicorn, app in stages/S8_product/app/ |
| later hosting (optional) |  | HF Space (Docker SDK, CPU); or any small VPS; weights pulled from a private HF; model repo via a token secret |  |
