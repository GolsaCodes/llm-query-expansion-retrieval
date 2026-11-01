# LLM-Based Query Expansion for Dense and Sparse Retrieval

## Overview

This project investigates the impact of Large Language Model (LLM)-based Query Expansion on information retrieval performance.

Using the **SciFact dataset** from the BEIR benchmark, we compare the retrieval performance of original queries and LLM-expanded queries across two retrieval approaches:

- **Dense Retrieval:** Semantic search using BAAI/bge-base-en-v1.5 embeddings and FAISS.
- **Sparse Retrieval:** Lexical search using BM25.

Query expansion is performed using **Gemini Flash 3.5**, which generates additional relevant terms and context to improve the retrieval effectiveness of the original queries.

## Project Structure

```text
.
├── dense_retrieval.ipynb
├── sparse_retrieval.ipynb
├── scifact_corpus_embeddings.npy
├── scifact_gemini_expanded_queries.csv
└── README.md
```

### Note
- **scifact_corpus_embeddings.npy** contains the embeddings for the corpus to avoid redundant execution, to be able to open the file when embeddings are needed.
- **scifact_gemini_expanded_queries.csv** contains the expanded queries using Gemini Flash 3.5. Expansions are saved in order to avoid redundancy.
## Dataset

- **Dataset:** SciFact
- **Benchmark:** BEIR
- **Task:** Scientific fact verification and document retrieval

The same dataset and evaluation metrics are used to compare the retrieval approaches.

## Methodology

### 1. Query Expansion

Gemini Flash 3.5 is used to expand the original queries by generating additional relevant information and terms.

The goal is to improve the retrieval process by providing richer query representations.

### 2. Dense Retrieval

Dense retrieval is implemented using:

- **Embedding Model:** `BAAI/bge-base-en-v1.5`
- **Vector Index:** FAISS
- **Retrieval Method:** Similarity-based dense retrieval

Two query settings are evaluated:

1. Original Query
2. Expanded Query

### 3. Sparse Retrieval

Sparse retrieval is implemented using **BM25**.

Three query settings are evaluated:

1. Original Query: Only the original query is used.
2. Expanded Query: Only the LLM-generated expanded query is used.
3. Original + Expanded Query: The original query is combined with the expanded query before retrieval.

This additional experiment investigates whether retaining the original query alongside the expanded content improves lexical matching.

## Evaluation Metrics

The retrieval systems are evaluated using:

- **Recall@5:** Proportion of relevant documents retrieved within the top 5 results.
- **Recall@10:** Proportion of relevant documents retrieved within the top 10 results.
- **MRR@10:** Mean Reciprocal Rank of the first relevant result within the top 10.
- **NDCG@10:** Normalized Discounted Cumulative Gain at rank 10.

## Results

### 1. Dense Retrieval (FAISS)

| Metric | Original Query | Expanded Query |
|---|---:|---:|
| Recall@5 | 0.8143 | **0.8196** |
| Recall@10 | 0.8669 | **0.8752** |
| MRR@10 | **0.7130** | 0.6995 |
| NDCG@10 | **0.7470** | 0.7380 |

**Analysis:**

Query expansion slightly improves Recall@5 and Recall@10, indicating that expanded queries retrieve a somewhat larger proportion of relevant documents.

However, MRR@10 and NDCG@10 decrease, suggesting that the expanded queries do not consistently rank the most relevant documents as highly as the original queries.

### 2. Sparse Retrieval (BM25)

| Metric | Original Query | Expanded Query | Original + Expanded |
|---|---:|---:|---:|
| Recall@5 | 0.6953 | 0.6994 | **0.7412** |
| Recall@10 | 0.7592 | 0.7979 | **0.8112** |
| MRR@10 | 0.5945 | 0.6033 | **0.6343** |
| NDCG@10 | 0.6307 | 0.6442 | **0.6725** |

**Analysis:**

Unlike dense retrieval, query expansion improves all four evaluation metrics in sparse retrieval.

Using only the expanded query provides modest improvements over the original query. However, combining the original and expanded queries achieves the best performance across all metrics.

This suggests that preserving the original query while adding relevant terms helps BM25 improve both document coverage and ranking quality.

### 3. Overall Comparison

The following table compares the original-query and expanded-query results for both retrieval approaches.

| Metric | Dense Original | Dense Expanded | BM25 Original | BM25 Original + Expanded |
|---|---:|---:|---:|---:|
| Recall@5 | **0.8143** | **0.8196** | 0.6953 | 0.7412 |
| Recall@10 | **0.8669** | **0.8752** | 0.7592 | 0.8112 |
| MRR@10 | **0.7130** | 0.6995 | 0.5945 | 0.6343 |
| NDCG@10 | **0.7470** | 0.7380 | 0.6307 | 0.6725 |

**Note:** The BM25 Original + Expanded setting is an additional experiment and does not have a directly corresponding Dense Retrieval experiment in the current implementation.

## Discussion

The experimental results reveal different effects of query expansion on dense and sparse retrieval.

- **Dense Retrieval:** Query expansion improves recall slightly but reduces ranking-oriented metrics (MRR and NDCG).
- **Sparse Retrieval:** Query expansion improves both recall and ranking quality, with the combination of original and expanded queries achieving the strongest results.

One possible explanation is that BM25 relies on lexical matching. Adding relevant terms can increase the likelihood of matching relevant documents. In contrast, dense retrieval already captures semantic similarities through embeddings, so additional query context may sometimes change the embedding representation in ways that do not improve ranking.

Overall, the results suggest that the effectiveness of LLM-based query expansion depends on the underlying retrieval method and how the expanded query is incorporated.

## Technologies Used

- Python
- Gemini Flash 3.5
- Hugging Face Transformers
- BAAI/bge-base-en-v1.5
- FAISS
- BM25
- BEIR / SciFact
- Pandas
- NumPy

## Conclusion

This project evaluates LLM-based query expansion across dense and sparse retrieval methods using the SciFact benchmark.

The findings show that query expansion can improve retrieval effectiveness, but its impact varies depending on the retrieval approach. While dense retrieval benefits slightly in recall, sparse retrieval shows improvements across all evaluated metrics, particularly when the original and expanded queries are combined.

These results highlight the importance of adapting query expansion strategies to the characteristics of different retrieval systems.
