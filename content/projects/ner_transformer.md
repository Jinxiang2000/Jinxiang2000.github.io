---
title: Named Entity Recognition with Transformers in PyTorch
date: 2025-06-20
draft: false
description: Fine-tuning DistilBERT for NER using the CoNLL-2003 dataset, handling label-token alignment and evaluating span-level accuracy.
tags: [“NLP”, “Transformers”, “Named Entity Recognition”, “PyTorch”, “HuggingFace”]
categories: [“Deep Learning”]
featuredImage: “assets/img/ner_transformer.jpg”
math: true
---
## Introduction

Named Entity Recognition (NER) is a fundamental task in Natural Language Processing (NLP) that involves identifying and classifying entities such as persons, organizations, locations, and miscellaneous names within raw text. In this project, we implemented a Transformer-based NER system using PyTorch and HuggingFace’s Transformers library, focusing on fine-tuning the DistilBERT model on the widely used CoNLL-2003 dataset.

Our objectives were threefold:
	1.	Build a high-performing NER model that correctly captures entity spans.
	2.	Explore how subword tokenization affects sequence labeling tasks.
	3.	Learn the practical aspects of PyTorch-based model training and evaluation in an NER setting.

This blog presents a detailed walkthrough of the model architecture, data preprocessing, training strategy, alignment logic, and evaluation techniques we employed.

## Dataset and Preprocessing

The CoNLL-2003 dataset contains annotations for four entity types: PERSON, ORGANIZATION, LOCATION, and MISC, using the BIO tagging scheme. Each sentence is tokenized, and each token is assigned a BIO tag.

We used HuggingFace’s datasets library to load and process the data. After tokenization via WordPiece, one critical preprocessing step was to align word-level BIO labels with the resulting subword tokens. Only the first subword receives the label, while others are masked from loss computation.

## Model Architecture

We used the distilbert-base-uncased model from HuggingFace, which is a lightweight version of BERT. It provides a good balance between speed and performance, making it suitable for fast experimentation.

We attached a linear layer on top of the Transformer to perform token-level classification:
```python
from transformers import AutoModelForTokenClassification

model = AutoModelForTokenClassification.from_pretrained(
    "distilbert-base-uncased",
    num_labels=len(label2id),
    id2label=id2label,
    label2id=label2id
)
```
The classification head predicts one of the BIO tags for each token.

Label Alignment Strategy

A key challenge in NER using transformers is dealing with subword tokenization. For example, the word “Washington” might be split into [“wash”, “##ington”]. We only apply the label to the first subword and mask the rest using -100 during loss computation:
```python
def align_labels_with_tokens(labels, word_ids):
    aligned_labels = []
    previous_word_id = None
    for word_id in word_ids:
        if word_id is None:
            aligned_labels.append(-100)
        elif word_id != previous_word_id:
            aligned_labels.append(labels[word_id])
        else:
            aligned_labels.append(-100)
        previous_word_id = word_id
    return aligned_labels
```
## Training

We used AdamW as the optimizer, combined with a linear learning rate scheduler with warm-up. The loss function is cross-entropy, and we experimented with both uniform and weighted loss to address class imbalance.

Training was conducted over multiple epochs, with evaluation performed on a validation split after each epoch.
```python
from transformers import TrainingArguments, Trainer

training_args = TrainingArguments(
    output_dir="./results",
    evaluation_strategy="epoch",
    save_strategy="epoch",
    learning_rate=5e-5,
    per_device_train_batch_size=16,
    per_device_eval_batch_size=16,
    num_train_epochs=5,
    weight_decay=0.01,
    logging_dir="./logs",
    logging_steps=10,
)
```
## Evaluation

We evaluated our model using the seqeval library, which calculates entity-level precision, recall, and F1-score:
```python
from seqeval.metrics import classification_report

print(classification_report(true_labels, predicted_labels))
```
Unlike token-wise accuracy, entity-level F1 ensures we only count correct predictions when both the boundaries and the class are correct. This is crucial in applications where partial credit is not acceptable.

## Results

The fine-tuned DistilBERT model achieved:
	•	F1 Score: 91.2%
	•	Precision: 92.5%
	•	Recall: 90.0%

This performance is competitive with larger models while benefiting from faster training and inference.

## Lessons Learned

This project revealed several nuanced aspects of building NER systems with Transformers:
	•	Token classification is more complex than it seems, especially due to subword issues.
	•	Proper alignment and loss masking is essential to avoid noisy gradients.
	•	Span-level metrics like F1 are the gold standard for evaluating NER.
	•	Lightweight models like DistilBERT are great for rapid iteration without compromising too much on accuracy.

## Future Work

Given more time, we would consider:
	•	Adding a CRF layer to model label transitions.
	•	Trying larger models like RoBERTa or domain-specific BERTs.
	•	Applying this pipeline to domain-specific datasets (e.g., clinical notes or legal contracts).
	•	Exporting the model to ONNX and optimizing it for inference in production environments.

## Code Snippets

Below is the key logic for loss masking and label alignment during preprocessing:
```python
def align_labels_with_tokens(labels, word_ids):
    aligned_labels = []
    previous_word_id = None
    for word_id in word_ids:
        if word_id is None:
            aligned_labels.append(-100)
        elif word_id != previous_word_id:
            aligned_labels.append(labels[word_id])
        else:
            aligned_labels.append(-100)
        previous_word_id = word_id
    return aligned_labels
```
You can find the full implementation and training notebook in the project GitHub repository.

⸻

This project served as a deep dive into token-level sequence modeling, fine-tuning transformer architectures for NER, and evaluating structured prediction models in NLP using PyTorch.