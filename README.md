# Few-Shot Colorectal Histopathology Image Classification

## Project Overview

This project investigates few-shot image classification for colorectal
histopathology using meta-learning.

The project uses the publicly available Colorectal Histology MNIST
dataset. Since the dataset does not provide explicit genetic mutation
labels, this project focuses on histopathology image classification
rather than mutation-level prediction.

## Objectives

- Develop a few-shot colorectal histopathology classification system.
- Evaluate established meta-learning approaches.
- Compare Prototypical Networks, Matching Networks, and MAML.
- Develop a novel dynamic prototype adaptation approach.
- Evaluate performance under different few-shot settings.

## Dataset

The primary dataset is the Colorectal Histology MNIST dataset.

Dataset source:

https://www.kaggle.com/datasets/kmader/colorectal-histology-mnist

The raw dataset is not stored in this repository because of dataset
size and repository storage limitations.

## Few-Shot Experimental Setup

The project will evaluate:

- 5-way 1-shot classification
- 5-way 5-shot classification
- 5-way 10-shot classification

The dataset will be divided at the class level into meta-training
and meta-testing classes.

## Baselines

The proposed approach will be compared against:

1. Prototypical Networks
2. Matching Networks
3. Model-Agnostic Meta-Learning (MAML)

## Proposed Method

The proposed method introduces dynamic prototype adaptation, where
support examples receive learned importance weights when constructing
class prototypes.

## Evaluation

The methods will be evaluated using:

- Accuracy
- Precision
- Recall
- Macro-F1
- Confidence intervals across test episodes

## Reproducibility

Experiments will use fixed random seeds and document:

- Dataset version
- Python version
- PyTorch version
- Model configuration
- Hyperparameters
- Number of episodes
- Random seed
