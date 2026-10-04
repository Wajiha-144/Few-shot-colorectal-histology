# Few-Shot Colorectal Histopathology Classification

## Track A — Research & Development

This project investigates few-shot colorectal histopathology image
classification using meta-learning.

The project focuses on comparative analysis, technical novelty,
ablation studies, and reproducibility.

## Research Question

Can dynamically adapted class prototypes improve few-shot colorectal
histopathology image classification compared with established
meta-learning approaches?

## Objectives

- Develop a reproducible few-shot histopathology classification pipeline.
- Implement established meta-learning baselines.
- Compare Prototypical Networks, Matching Networks, and MAML.
- Develop a Dynamic Prototype Adaptation approach.
- Conduct ablation studies to evaluate the contribution of the proposed
  components.
- Evaluate performance under different few-shot settings.

## Dataset

The project uses the publicly available Colorectal Histology MNIST
dataset.

Dataset source:

https://www.kaggle.com/datasets/kmader/colorectal-histology-mnist

The dataset contains histopathology image classes rather than explicit
genetic mutation labels. Therefore, this project focuses on
few-shot histopathology classification rather than mutation prediction.

Raw image files are not stored in this repository.

## Experimental Design

The project will evaluate few-shot classification under:

- 5-way 1-shot
- 5-way 5-shot
- 5-way 10-shot

The dataset will be divided at the class level into meta-training and
meta-testing classes.

## Baselines

The proposed method will be compared against:

1. Prototypical Networks
2. Matching Networks
3. Model-Agnostic Meta-Learning (MAML)

## Proposed Method

The proposed approach introduces Dynamic Prototype Adaptation.

Instead of assigning equal importance to every support example when
constructing a class prototype, the method learns importance weights
for support examples and uses these weights to construct an adaptive
prototype.

## Ablation Study

The contribution of the proposed components will be investigated by
comparing:

- Standard mean prototypes
- Dynamically weighted prototypes
- Different prototype adaptation configurations

## Evaluation

Models will be evaluated using:

- Accuracy
- Precision
- Recall
- Macro-F1
- 95% confidence intervals

Multiple test episodes will be used to obtain reliable estimates of
few-shot performance.

## Reproducibility

Experiments will document:

- Dataset source and version
- Python version
- PyTorch version
- Random seed
- Image preprocessing
- Model architecture
- Hyperparameters
- Number of episodes
- Few-shot configuration
- Evaluation procedure

## Project Structure

```text
data/          Dataset metadata and documentation
notebooks/     Exploratory and experimental notebooks
src/           Source code
configs/       Experiment configurations
experiments/   Experimental runs
results/       Tables, figures, and logs
paper/         Technical paper
