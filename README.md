````markdown
# A RAG-Based Expert System for Laptop Recommendation

## Overview

Choosing the right laptop can be difficult because users often need to compare a large number of products based on price, brand, processor, RAM, storage, GPU, category, and other specifications.

This project develops a **RAG-Based Expert System for Laptop Recommendation** that allows users to search for laptops using natural language queries and receive recommendations based on their specific requirements.

The system combines data processing, feature engineering, near-duplicate detection, semantic embeddings, vector storage, metadata filtering, vector similarity search, and Retrieval-Augmented Generation (RAG) to retrieve relevant laptops and generate recommendations from the retrieved information.

---

## Problem Statement

Searching for a suitable laptop often requires manually checking and comparing many products across different specifications and price ranges.

A user may have requirements such as:

- A specific budget
- A preferred brand
- A particular laptop category
- Minimum RAM
- Required storage
- CPU requirements
- GPU requirements

The goal of this project is to make this process easier by allowing users to describe their requirements in natural language and retrieve laptops that match those requirements.

---

## Project Objectives

The main objectives of the project are:

- Collect and prepare laptop information from different sources.
- Clean and organize laptop specifications for further processing.
- Detect near-duplicate laptop records.
- Convert laptop specifications into semantic vector representations.
- Store embeddings and metadata for retrieval.
- Apply structured filtering based on user requirements.
- Retrieve relevant laptops using vector similarity search.
- Generate recommendations using retrieved laptop information.
- Evaluate different FAISS indexing approaches based on retrieval performance and efficiency.
- Provide the recommendation system through a web application.

---

# Data Collection

The project uses laptop information from two sources:

1. **Public laptop dataset**
2. **Web-scraped laptop data**

The collected information contains laptop specifications that are used throughout the data processing and recommendation pipeline.

The combination of these sources provides a broader set of laptop information for the system.

---

# Data Processing Pipeline

The collected data is processed before being used for semantic retrieval.

The overall data preparation process is:

```text
Public Dataset
      +
Web Scraped Data
      │
      ▼
Data Cleaning
      │
      ▼
Feature Engineering
      │
      ▼
Full Specification Text
      │
      ▼
N-gram Processing
      │
      ▼
MinHash LSH
      │
      ▼
Near-Duplicate Detection
      │
      ▼
Processed Laptop Dataset
      │
      ▼
Parquet Storage
````

## Data Cleaning

The collected laptop data is cleaned and prepared so that the specifications can be used consistently during the later stages of the system.

---

## Feature Engineering

Relevant laptop attributes are combined to create a meaningful representation of each laptop.

The specification information includes attributes such as:

* Brand
* Product name
* Category
* CPU
* RAM
* Storage
* GPU
* Display information
* Price
* Other available specifications

These attributes are used to create a full specification text for semantic processing.

---

## N-gram Processing

N-grams are used during the text processing stage to represent combinations of terms from the laptop specification text.

This helps preserve meaningful combinations of words and specifications within the processed text.

---

# Near-Duplicate Detection

The project uses **MinHash LSH (Locality-Sensitive Hashing)** to identify near-duplicate laptop records.

N-gram representations are used in the duplicate detection process.

The purpose is to identify records that contain highly similar specification information and avoid treating near-duplicate records as completely different products.

---

# Data Storage

The processed data is stored in **Parquet format**.

Parquet is used as the processed data format within the data processing workflow.

The project also uses **ChromaDB** for storing vector embeddings and associated metadata used during retrieval.

---

# Semantic Embeddings

After data processing, the laptop specification text is converted into semantic embeddings using the Sentence Transformer model:

```text
all-MiniLM-L6-v2
```

The model produces **384-dimensional embeddings** for the laptop text representations.

These embeddings allow the system to compare laptop information and user queries based on semantic similarity rather than only exact keyword matching.

---

# ChromaDB

The generated embeddings and laptop metadata are stored in **ChromaDB**.

The stored information is used to support the retrieval process and to associate vector representations with the corresponding laptop information.

The stored data includes:

* Embeddings
* Text information
* Laptop metadata

---

# Recommendation Workflow

The recommendation process starts when a user submits a natural-language query.

For example:

```text
I need a gaming laptop under €1500 with 16GB RAM.
```

The system processes the query and identifies relevant requirements.

The overall recommendation workflow is:

```text
User Natural-Language Query
            │
            ▼
      Query Processing
            │
            ▼
Requirement Extraction
            │
            ▼
    Metadata Filtering
            │
            ▼
   Candidate Laptops
            │
            ▼
 Semantic Query Embedding
            │
            ▼
     FAISS Retrieval
            │
            ▼
 Relevant Laptop Results
            │
            ▼
 Retrieved Laptop Context
            │
            ▼
        Groq LLM
            │
            ▼
     Final Recommendation
```

---

# Metadata Filtering

Before performing vector retrieval, the system applies metadata-based filtering according to the requirements extracted from the user's query.

The filtering can consider requirements such as:

* Brand
* Price
* Category
* RAM
* Storage
* CPU
* GPU

This reduces the candidate set before performing semantic vector retrieval.

---

# FAISS Vector Search

The project uses **FAISS (Facebook AI Similarity Search)** for vector similarity search.

The user's query is converted into an embedding using the same embedding model used for the laptop data.

The system then performs similarity search against the relevant candidate vectors.

The retrieved laptops are ranked according to their vector similarity to the query.

---

# Query-Specific Retrieval

After metadata filtering, the system creates a retrieval space from the remaining candidate laptops.

FAISS is then used to search within this candidate set rather than treating every laptop as an equally suitable candidate.

This combines:

```text
Structured Requirements
        +
Semantic Similarity
        │
        ▼
Relevant Laptop Retrieval
```

---

# Retrieval-Augmented Generation

After retrieving relevant laptops, the retrieved laptop information is provided to a **Groq Large Language Model (LLM)** as context.

The LLM uses the retrieved information to generate the final recommendation.

The recommendation process is therefore:

```text
User Query
    ↓
Filtering
    ↓
Vector Retrieval
    ↓
Relevant Laptop Information
    ↓
Groq LLM
    ↓
Recommendation
```

The generated recommendation is based on the retrieved laptop information rather than requiring the user to manually compare all retrieved specifications.

---

# FAISS Index Comparison

An important part of the project is the comparison of different FAISS indexing methods.

The project implements and evaluates:

### 1. IndexFlatL2

An exact nearest-neighbor search method used as the baseline.

### 2. IVF

An inverted-file based approximate nearest-neighbor approach.

### 3. Product Quantization (PQ)

A vector compression approach used for approximate similarity search.

### 4. IVF + PQ

A combination of inverted-file indexing and product quantization.

### 5. HNSW

A graph-based approximate nearest-neighbor search method.

---

# Retrieval Evaluation

The different FAISS indexing methods are compared based on retrieval quality and efficiency.

The evaluation considers:

* **Precision@5**
* **Recall@5**
* **Training Time**
* **Inference Latency**

This comparison provides an understanding of the trade-offs between retrieval accuracy and search efficiency across different indexing approaches.

---

# Web Application

The recommendation system is implemented as a **Flask-based web application**.

The application allows users to interact with the recommendation system through a web interface and submit natural-language laptop queries.

The application connects the user query with the retrieval and recommendation pipeline.

The Flask application was made externally accessible using **Ngrok** during development and demonstration.

---

# End-to-End System Architecture

The complete project workflow can be summarized as:

```text
                  DATA SOURCES
                       │
          ┌────────────┴────────────┐
          │                         │
   Public Dataset             Web Scraping
          │                         │
          └────────────┬────────────┘
                       ▼
                Data Cleaning
                       │
                       ▼
               Feature Engineering
                       │
                       ▼
              Full Specification Text
                       │
                       ▼
                 N-gram Processing
                       │
                       ▼
               MinHash LSH
                       │
                       ▼
            Near-Duplicate Detection
                       │
                       ▼
               Processed Dataset
                       │
                       ▼
                Parquet Storage
                       │
                       ▼
             all-MiniLM-L6-v2
                       │
                       ▼
              Semantic Embeddings
                       │
                       ▼
                   ChromaDB
                       │
                       │
                 USER QUERY
                       │
                       ▼
                Query Processing
                       │
                       ▼
             Requirement Extraction
                       │
                       ▼
              Metadata Filtering
                       │
                       ▼
             Candidate Laptop Set
                       │
                       ▼
                FAISS Retrieval
                       │
                       ▼
              Relevant Laptops
                       │
                       ▼
               Retrieved Context
                       │
                       ▼
                  Groq LLM
                       │
                       ▼
              Final Recommendation
                       │
                       ▼
             Flask Web Application
                       │
                       ▼
                    Ngrok
```

---

# Technology Stack

## Programming

* Python

## Data Processing

* Apache Spark
* Pandas
* NumPy
* Parquet

## Text Processing and Duplicate Detection

* N-grams
* MinHash LSH

## Embeddings

* Sentence Transformers
* all-MiniLM-L6-v2

## Vector Database

* ChromaDB

## Vector Search

* FAISS
* IndexFlatL2
* IVF
* Product Quantization (PQ)
* IVF + PQ
* HNSW

## Large Language Model

* Groq LLM

## Web Application

* Flask

## Development and Access

* Google Colab
* Ngrok

---

# Repository Structure

```text
A-RAG-Based-Expert-System-for-Laptop-Recommendation/
│
├── Code.ipynb
│
├── public_dataset.csv
│
├── scrapped.csv
│
├── Report.pdf
│
└── README.md
```

### Files

**Code.ipynb**
Contains the complete implementation of the project.

**public_dataset.csv**
Contains the public laptop dataset used in the project.

**scrapped.csv**
Contains laptop information collected through web scraping.

**Report.pdf**
Contains the detailed project report and documentation.

**README.md**
Provides an overview of the project, workflow, technologies, and implementation.

---

# Example Query

A user can enter a query such as:

```text
I need a gaming laptop under €1500 with 16GB RAM and a good GPU.
```

The system processes the request, filters the available laptops based on the extracted requirements, performs vector retrieval, and uses the retrieved laptop information to generate a recommendation.

---

# Real-World Application

The system is designed to reduce the effort involved in manually searching and comparing laptop products.

Instead of checking multiple products individually, users can describe what they need in natural language and receive relevant laptop options based on their:

* Budget
* Specifications
* Brand preferences
* Laptop category
* Intended requirements

This makes the laptop selection process more convenient and easier to navigate.

---

# Security

API credentials and secret keys are not included in the repository.

The project uses external services that require their own credentials, including the Groq API and Ngrok.

Users should configure their own credentials when running the complete application.

---

# Conclusion

This project brings together data processing, semantic representation, vector databases, similarity search, approximate nearest-neighbor indexing, metadata filtering, and Retrieval-Augmented Generation into a single laptop recommendation system.

It demonstrates how structured laptop information can be processed and retrieved efficiently and then used as context for generating recommendations through an LLM.

```
