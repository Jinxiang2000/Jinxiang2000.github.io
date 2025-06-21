---
title: "RAG-Powered Code Search: A Course Project on Semantic Code Retrieval"
date: 2025-06-20
description: "A comprehensive exploration of combining retrieval-augmented generation with semantic search for intelligent code discovery - developed as part of my Information Retrieval coursework."
tags: ["FAISS", "GPT", "RAG", "CodeSearchNet", "Information Retrieval", "Semantic Search", "FastAPI", "Streamlit", "Course Project"]
---

## Project Overview

As part of my Information Retrieval course, I tackled a fundamental challenge in software development: **how can we make code search more intelligent and context-aware?** Traditional keyword-based search often fails to capture the semantic intent behind developer queries, leading to frustrating experiences when trying to find relevant code examples.

This project explores the application of **Retrieval-Augmented Generation (RAG)** to code search, combining the power of semantic embeddings with large language models to create a more intuitive code discovery experience.

## Problem Statement & Motivation

The challenge was to develop a system that could bridge the gap between natural language queries and relevant code snippets. Existing approaches suffer from several limitations:

- **Keyword dependency**: Traditional search relies on exact token matching
- **Lack of semantic understanding**: Queries like "sort a list" and "arrange elements in order" should return similar results
- **Context insensitivity**: Results often lack the specific context developers need

**My approach**: Implement a RAG-based system that combines semantic retrieval with generative refinement to provide contextually relevant, complete code solutions.

## Technical Architecture

The system architecture consists of several key components working together:

### The Data Foundation
- **CodeSearchNet dataset**: 400k+ Python functions with docstrings from real GitHub repos
- **Semantic embeddings**: `all-MiniLM-L6-v2` to understand code intent beyond keywords
- **FAISS index**: Lightning-fast similarity search through the entire corpus

### The Retrieval Engine
```python
# Core retrieval logic
def semantic_search(query, k=5):
    query_embedding = model.encode([query])
    distances, indices = faiss_index.search(query_embedding, k)
    return [code_samples[idx] for idx in indices[0]]
```

### The Generation Layer
- **GPT-3.5-turbo**: Takes retrieved examples and synthesizes clean, complete code
- **Smart prompting**: Combines user intent with relevant examples for better output

### The Interface
- **FastAPI backend**: RESTful API handling embedding, retrieval, and generation workflows
- **Streamlit frontend**: Interactive web interface for query input and result visualization  
- **Caching layer**: TTL-based caching using `cachetools` for improved response times

## Methodology & Implementation

### Step 1: Understanding the Query
When you type "build a KNN face recognizer", the system:

1. **Embeds your query** using MiniLM (384-dimensional vector)
2. **Searches the FAISS index** for semantically similar code
3. **Ranks results** by cosine similarity

### Step 2: Smart Retrieval
Instead of just matching keywords, the system finds code that actually does what you want:

```python
# Example retrieved function
def train_face_recognizer(image_dir):
    """Train KNN classifier for face recognition from directory"""
    encodings = []
    labels = []
    
    for person_name in os.listdir(image_dir):
        person_dir = os.path.join(image_dir, person_name)
        for image_file in os.listdir(person_dir):
            # Extract face encodings...
```

### Step 3: Generation and Refinement
The retrieved examples go to GPT-3.5 with a carefully crafted prompt:

```
Query: "Train a KNN for face recognition from a directory of images"

Here are some relevant code examples:
[retrieved snippets]

Generate a complete, clean Python function that addresses the query.
Include proper imports, error handling, and clear variable names.
```

## The Results: Numbers Don't Lie

I tested this against traditional approaches and the results were impressive:

| Method | MAP@5 | NDCG@5 | Response Time |
|--------|-------|--------|---------------|
| Keyword Search | 0.23 | 0.50 | ~0.1s |
| BM25 | 0.27 | 0.55 | ~0.2s |
| **My RAG System** | **0.43** | **0.79** | **~0.9s** |

### Real Example: Face Recognition Query

**Input**: *"Train a KNN for face recognition from a directory of images"*

**Output**:
```python
import os
import face_recognition
from sklearn.neighbors import KNeighborsClassifier
import pickle

def train_face_recognizer(image_directory, model_save_path=None):
    """
    Train a KNN classifier for face recognition from labeled image directory.
    
    Args:
        image_directory: Path to directory with subdirs for each person
        model_save_path: Optional path to save trained model
    
    Returns:
        Trained KNeighborsClassifier
    """
    encodings = []
    labels = []
    
    # Process each person's directory
    for person_name in os.listdir(image_directory):
        person_path = os.path.join(image_directory, person_name)
        if not os.path.isdir(person_path):
            continue
            
        # Process each image
        for image_file in os.listdir(person_path):
            if image_file.lower().endswith(('.jpg', '.jpeg', '.png')):
                image_path = os.path.join(person_path, image_file)
                image = face_recognition.load_image_file(image_path)
                
                # Extract face encodings
                face_encodings = face_recognition.face_encodings(image)
                if face_encodings:
                    encodings.append(face_encodings[0])
                    labels.append(person_name)
    
    # Train KNN classifier
    knn_classifier = KNeighborsClassifier(n_neighbors=3, algorithm='ball_tree')
    knn_classifier.fit(encodings, labels)
    
    # Save model if path provided
    if model_save_path:
        with open(model_save_path, 'wb') as f:
            pickle.dump(knn_classifier, f)
    
    return knn_classifier
```

This generated code demonstrates the system's ability to synthesize coherent, well-documented functions that address the specific query requirements.

## Results & Analysis

### What Works Really Well
- **Semantic understanding**: The system gets what you want, even with vague descriptions
- **Code quality**: Generated snippets are clean and well-structured
- **Speed**: Sub-second responses for most queries

### Current Limitations
- **Dataset bias**: Heavily skewed toward certain types of functions
- **Context limits**: GPT sometimes truncates longer code examples
- **No execution validation**: Generated code isn't tested for correctness

### Key Insights
The hybrid approach of combining retrieval with generation addresses the limitations of each individual method. Retrieval-only systems provide relevant but often incomplete examples, while generation-only approaches may produce syntactically correct but semantically inaccurate code. The RAG approach leverages the strengths of both paradigms.

## Future Work & Extensions

Building on the foundation established in this course project, several directions for improvement emerge:

### Short Term
- **Multi-language support**: Extending beyond Python to JavaScript, Go, etc.
- **Better evaluation**: Testing generated code for functional correctness
- **Improved caching**: Smarter cache invalidation and pre-computation

### Long Term
- **IDE integration**: VS Code extension for in-line suggestions
- **GraphRAG**: Understanding relationships between code components
- **Custom model training**: Fine-tuning on code-specific tasks

## Implementation & Reproducibility

The complete implementation is available on GitHub with detailed documentation and setup instructions:

**Repository**: [SI650 Course Project - RAG Code Search](https://github.com/Jinxiang2000/SI650-Project/tree/main)

To reproduce the results:

```bash
git clone https://github.com/Jinxiang2000/SI650-Project.git
cd SI650-Project
pip install -r requirements.txt
streamlit run app.py
```

## Conclusion

This course project successfully demonstrates the potential of RAG-based approaches for semantic code search. By combining dense retrieval with generative refinement, we achieved significant improvements over traditional keyword-based methods:

- **43% improvement in MAP@5** compared to keyword search
- **79% NDCG@5 score** indicating strong ranking quality  
- **Sub-second response times** maintaining practical usability

The work highlights the importance of semantic understanding in information retrieval systems and showcases how modern NLP techniques can be applied to domain-specific challenges in software engineering.

**Key Takeaways:**
- Semantic embeddings significantly outperform lexical matching for code search
- RAG architectures effectively bridge the gap between retrieval and generation
- User experience considerations are crucial for practical IR system deployment

---

*This project was completed as part of SI 650: Information Retrieval at the University of Michigan. The complete codebase and experimental details are available in the [project repository](https://github.com/Jinxiang2000/SI650-Project/tree/main).*
