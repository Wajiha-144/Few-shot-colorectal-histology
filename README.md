# Few-Shot Colorectal Histopathology Classification

**Track A: Research & Development** | Group ID: F26-04

This project investigates few-shot colorectal histopathology image classification using meta-learning. It focuses on comparative analysis, technical novelty, ablation studies and reproducibility.

## Research Question

Can Dynamic Prototype Adaptation (DPA), using an ImageNet-pretrained ResNet-18 backbone, improve few-shot colorectal histopathology image classification compared with established meta-learning methods and a fine-tuning baseline in 1-shot, 5-shot, and 10-shot settings?

**Secondary question:Under the same backbone initialization and evaluation episodes, how do meta-learning approaches compare with fine-tuning, and does the proposed DPA method reduce any observed performance gap?

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
Class Splits
The dataset contains eight tissue classes. Each experiment uses four classes for training and four different classes for few-shot testing.

Split A: Primary experiment.
Splits B and C: Additional experiments to assess whether the results generalize across different choices of base and novel classes.

The predefined Phase 2 class splits will be used without changing them based on observed results. A stratified 20% of base-class images will be reserved for validation. Novel-class images will not be used to train the base classifier or select model settings.

Few-Shot Evaluation Protocol
Each test episode contains four tissue classes and 15 query images per class. The classifier receives 1, 5, or 10 labelled support images per class and predicts the labels of the query images.
The planned evaluation uses 600 test episodes per shot setting per split. The episode files generated during Phase 2 will be reused so that methods are evaluated on matching tasks. Where the protocol specifies nested support sets, the 1-shot support set will be contained within the 5-shot set, which will be contained within the 10-shot set. Query images will remain the same across these shot settings within each episode.
At least three independent training seeds will be used per method. Episode sampling seeds and model-training seeds will be recorded separately.

## Baselines

All methods will use a shared ResNet-18 backbone initialized with ImageNet-pretrained weights wherever compatible with each method's training procedure. Methods will be evaluated using the same fixed class splits and saved episodes to ensure fair comparison.
1. **Prototypical Networks (ProtoNet):** Classifies query images by comparing their features with the average feature representation of each class.
2. **Matching Networks:** Uses similarities between query images and individual labelled support examples to make predictions.
3. **Relation Networks:** Learns a relation score to determine how well a query image matches each class representation.
4. **MAML / ANIL:** Uses meta-learning to prepare the model to adapt to new classes from a small number of examples. The selected variant will be documented.
5. **Fine-tuning baseline:** Further trains the pretrained model using the available labelled support examples. The implementation (Baseline++ or linear probe) will be selected and documented.
6. **Dynamic Prototype Adaptation (DPA):** The proposed method, combining weighted prototypes, query-conditioned adaptation, and the typicality and consistency loss terms.

The main comparison will evaluate all implemented methods in 4-way 1-shot, 5-shot, and 10-shot settings. Results will include accuracy, precision, recall, macro-F1, and 95% confidence intervals. The same saved evaluation episodes will be used for paired comparisons wherever applicable.

## Backbone

The primary backbone is ImageNet-pretrained ResNet-18. The same architecture and agreed initial weights will be used across the comparison methods where their training procedures permit.

## Proposed Method: Dynamic Prototype Adaptation (DPA)

Standard prototypes average all support embeddings equally, so one atypical support image can distort the prototype. DPA has two parts:

- **Weighted prototype:** learned importance weights for support examples (useful when K > 1).
- **Query-conditioned adaptation:** a lightweight adapter that shifts the prototype using the query, so the method also works in 1-shot, where weighting alone has no effect.

It is trained with a custom loss: episodic cross-entropy, plus a typicality term that penalises giving weight to support samples far from their class, plus a consistency term that keeps the prototype stable when support images are replaced with synthetic atypical samples (strong stain shift, blur or pasted patches).

The weighted-prototype idea exists in prior work, so the contribution claimed here is the combination of query-conditioned adaptation with the loss that trains it. Related work will be reviewed fully in the technical paper.

## Ablation Study
The ablation study will investigate which components of Dynamic Prototype Adaptation (DPA) contribute to its performance, using the same ResNet-18 backbone and evaluation protocol.
Standard vs. weighted prototypes: Compare ordinary class prototypes with prototypes that assign different importance to support examples.
Contribution of the custom loss: Compare DPA trained with and without the typicality term and consistency term, including each term separately.
Query-conditioned vs. support-only adaptation: Test whether allowing the query image to influence the class representation improves classification.
Shot-setting analysis: Compare the contribution of each component under 1-shot, 5-shot, and 10-shot settings.

## Evaluation

The following metrics will be reported:

Accuracy: The proportion of query images classified correctly.
Precision: How often predictions for a class are correct.
Recall: How many actual examples of a class are correctly identified.
Macro-F1: The average F1 score across classes, giving each class equal importance.
95% confidence intervals: To describe uncertainty around the estimated performance.

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
