# Few-Shot Colorectal Histopathology Classification

**Track A: Research & Development** | Group ID: F26-04

This project investigates few-shot colorectal histopathology image classification using meta-learning. It focuses on comparative analysis, technical novelty, ablation studies and reproducibility.

## Research Question

Can a query-conditioned prototype, trained with a loss that penalises atypical support samples (Dynamic Prototype Adaptation), improve few-shot colorectal histopathology classification compared with established meta-learning and fine-tuning approaches, including in the 1-shot setting?

**Secondary question:** Under identical backbones and episodes, why do meta-learning methods often match or trail simple fine-tuning on histology, and does the proposed loss narrow that gap?

## Objectives

- Build a reproducible few-shot histopathology classification pipeline.
- Implement established baselines with a shared backbone and shared episodes.
- Compare Prototypical Networks, Matching Networks, Relation Networks, MAML/ANIL and a fine-tuning baseline.
- Develop Dynamic Prototype Adaptation (DPA).
- Run ablation studies to measure the contribution of each component.
- Evaluate under 1-, 5- and 10-shot settings.

## Dataset

The project uses: Colorectal Histology MNIST on Kaggle, which mirrors Kather et al. (2016).

- Source: https://www.kaggle.com/datasets/kmader/colorectal-histology-mnist
- Original paper: Kather, J. N. et al. (2016). *Multi-class texture analysis in colorectal cancer histology.* Scientific Reports, 6, 27988.
- Files used: the **150×150 RGB tiles** (5,000 images, 8 tissue classes, 625 per class). The 28×28 and 64×64 CSV versions are not used.
- Model input: resized to 224×224 and ImageNet-normalised on the fly.

The dataset has tissue-type labels, not genetic mutation labels. The project therefore addresses few-shot tissue classification, not mutation prediction.

Raw image files are not stored in this repository (`data/raw/` is gitignored). The Phase 2 notebook locates or downloads them.

## Experimental Design

Eight classes cannot support a valid 5-way few-shot test with disjoint base and novel classes (only 3 base classes would remain), and a single dataset rules out a cross-domain setup. The protocol is therefore **in-domain, with 4 base and 4 novel classes**:

- 4-way 1-shot
- 4-way 5-shot
- 4-way 10-shot

| Split | Base classes (training) | Novel classes (testing) |
|---|---|---|
| A (primary) | 2, 5, 7, 8 | 1, 3, 4, 6 |
| B (alternate) | 1, 4, 6, 8 | 2, 3, 5, 7 |
| C (alternate) | 3, 4, 7, 8 | 1, 2, 5, 6 |

Class labels: 1 Tumour epithelium, 2 Simple stroma, 3 Complex stroma, 4 Immune cells (lympho), 5 Debris and mucus, 6 Normal mucosal glands, 7 Adipose tissue, 8 Background.

- Splits are fixed before training and are not changed after seeing results. Split A is the primary result; B and C check robustness.
- A stratified 20% of each base class's images is held out for validation and model selection. Novel-class images are never used for training or selection.
- **Test episodes:** 600 per shot setting per split, 15 query images per class, episode seeds `10000 + i`. The class set and queries are identical across 1-, 5- and 10-shot, and supports are nested, so all methods see the same tasks.
- **Validation episodes:** 300 per shot setting, seeds `20000 + i`.

## Baselines

All methods share the same backbone, pretrained weights and episodes:

1. Prototypical Networks (ProtoNet)
2. Matching Networks
3. Relation Networks
4. MAML / ANIL
5. Fine-tuning baseline (Baseline++ / linear probe)

The fine-tuning baseline is included to test why meta-learning may underperform simple fine-tuning.

## Backbone

Primary: ViT-Small/16 with self-supervised DINO ImageNet weights. Comparison: ResNet-50 (ImageNet).

## Proposed Method: Dynamic Prototype Adaptation (DPA)

Standard prototypes average all support embeddings equally, so one atypical support image can distort the prototype. DPA has two parts:

- **Weighted prototype:** learned importance weights for support examples (useful when K > 1).
- **Query-conditioned adaptation:** a lightweight adapter that shifts the prototype using the query, so the method also works in 1-shot, where weighting alone has no effect.

It is trained with a custom loss: episodic cross-entropy, plus a typicality term that penalises giving weight to support samples far from their class, plus a consistency term that keeps the prototype stable when support images are replaced with synthetic atypical samples (strong stain shift, blur or pasted patches).

The weighted-prototype idea exists in prior work, so the contribution claimed here is the combination of query-conditioned adaptation with the loss that trains it. Related work will be reviewed fully in the technical paper.

## Ablation Study

- Standard mean prototypes vs. dynamically weighted prototypes
- With vs. without the custom loss (typicality and consistency terms separately)
- Query-conditioned vs. support-only weighting
- ViT vs. CNN backbone

## Evaluation

- Metrics: accuracy, precision, recall and macro-F1, computed per episode and then averaged.
- 95% confidence intervals over test episodes; paired tests on shared episodes for DPA versus each baseline.
- 3 or more independent training seeds per method.

## Reproducibility

Experiments will document:

- Dataset source and version
- Python and PyTorch versions
- Random seeds (global seed 42; episode seeds as above)
- Image preprocessing
- Class splits and saved episode files
- Model architecture and hyperparameters
- Number of episodes
- Few-shot configuration
- Evaluation procedure

All external code, datasets and pretrained weights are cited.

## Project Status

| Phase | Status |
|---|---|
| Phase 1: Problem definition | Done |
| Phase 2: Data acquisition and preparation | Notebook and report complete (`notebooks/01_phase2_data_preparation.ipynb`) |
| Phase 3: Baselines, DPA and experiments | Planned |

## How to Reproduce Phase 2

```bash
pip install -r requirements.txt
export KATHER_DATA=/path/to/colorectal-histology-mnist
jupyter notebook notebooks/01_phase2_data_preparation.ipynb
```

If `KATHER_DATA` is not set, the notebook looks in `data/raw` and then tries `kagglehub` (this needs Kaggle credentials). Running the notebook top to bottom regenerates the index, splits, episodes, figures and summary.

## Project Structure

```text
data/
  raw/            Kaggle files 
  processed/      index_clean.csv
splits/           class_splits.json, image_roles.csv,
                  test_episodes_{A,B,C}.json, val_episodes_{A,B,C}.json
notebooks/        F26-04.ipynb
results/          Tables, summaries and logs
figures/          Figures
requirements.txt  Python dependencies
```

## References

- Kather, J. N. et al. (2016). Multi-class texture analysis in colorectal cancer histology. *Scientific Reports*, 6, 27988.
- Shakeri, F. et al. (2022). FHIST: A Benchmark for Few-shot Classification of Histological Images. arXiv:2206.00092.
- Dataset mirror: https://www.kaggle.com/datasets/kmader/colorectal-histology-mnist
