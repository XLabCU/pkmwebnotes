---
title: "01: Vector Space Models and TF-IDF"
created: 2025-08-05T12:57:24.739Z
tags: [advanced]
---
# DH Advanced - 01: Vector Space Models and TF-IDF

While simple word counts and frequency distributions give us some insight, they treat all words equally. However, some words carry more informational weight than others in distinguishing documents or characterizing content. **TF-IDF** is a numerical statistic that reflects how important a word is to a document within a collection or corpus.

It's a cornerstone of the **Vector Space Model (VSM)** approach to text analysis, where documents are represented as numerical vectors in a high-dimensional space. Each dimension corresponds to a unique word (or term) in the corpus vocabulary.

**Core Concepts:**

1.  **Term Frequency (TF):** How often does a term `t` appear in a document `d`?
    *   There are various ways to calculate TF:
        *   **Raw Count:** `TF(t, d)` = Number of times term `t` appears in document `d`.
        *   **Boolean Frequency:** `TF(t, d)` = 1 if `t` occurs in `d`, 0 otherwise.
        *   **Log Normalization:** `TF(t, d)` = `log(1 + raw_count)`. (Dampens effect of very high counts).
        *   **Augmented Frequency:** `TF(t, d)` = `0.5 + (0.5 * raw_count) / max_freq_in_doc`. (Normalizes by max frequency in the document).
    *   *Higher TF suggests the term is important within that specific document.*

2.  **Inverse Document Frequency (IDF):** How common or rare is a term `t` across the entire corpus `D`?
    *   `IDF(t, D)` = `log( N / (df_t + 1) )` where:
        *   `N` = Total number of documents in the corpus.
        *   `df_t` = Document Frequency of term `t` (number of documents containing term `t`).
        *   `+1` in the denominator prevents division by zero if a term appears in all documents. Sometimes smoothed differently.
    *   *IDF is high for rare terms and low for common terms (like stop words, although they are often removed beforehand).* It measures the *informativeness* of a term.

3.  **TF-IDF Score:** The product of TF and IDF.
    *   `TF-IDF(t, d, D) = TF(t, d) * IDF(t, D)`
    *   *The TF-IDF score is high when a term appears frequently in a specific document (high TF) but rarely in the overall corpus (high IDF). It's low when the term is common across the corpus or rare within the document.*

**Implementation with `scikit-learn`:**

The `scikit-learn` library provides efficient tools for calculating TF-IDF.

**Setup:**

Let's create a small corpus of sample documents. In practice, this would be a list of strings, where each string is the content of a document (e.g., fetched OCR text, chapter content).

```python
# Sample corpus (list of documents)
# Imagine these are pre-processed (lowercased, punctuation removed, maybe stemmed/lemmatized)
doc1 = "digital humanities combines computing analysis humanities research"
doc2 = "computing methods allow analysis large text corpora humanities"
doc3 = "digital methods research expands humanities scope"
doc4 = "text analysis computing focus digital humanities"

corpus = [doc1, doc2, doc3, doc4]

print("--- Sample Corpus ---")
for i, doc in enumerate(corpus):
    print(f"Doc {i+1}: {doc}")
```

**Calculating TF-IDF:**

```python
from sklearn.feature_extraction.text import TfidfVectorizer
import pandas as pd

# 1. Initialize the TfidfVectorizer
#    Common parameters:
#    - stop_words='english' : Use built-in English stop words
#    - max_df=0.85        : Ignore terms that appear in > 85% of docs (corpus-specific stop words)
#    - min_df=1           : Ignore terms that appear in < 1 document (or specify minimum count)
#    - ngram_range=(1, 1) : Consider only single words (unigrams). (1, 2) for unigrams and bigrams.
#    - use_idf=True       : Enable IDF weighting (True by default)
#    - smooth_idf=True    : Add 1 to document frequencies (prevents zero division)
#    - norm='l2'          : Normalize document vectors to unit length (L2 norm) - common practice

vectorizer = TfidfVectorizer(stop_words=None, smooth_idf=True, norm='l2') # Keep simple for demo

# 2. Fit the vectorizer to the corpus and transform the corpus into a TF-IDF matrix
#    .fit() learns the vocabulary and IDF weights
#    .transform() creates the TF-IDF vectors for each document
tfidf_matrix = vectorizer.fit_transform(corpus)

# The output 'tfidf_matrix' is a sparse matrix (efficient for many zeros)
print("\n--- TF-IDF Matrix (Sparse Representation) ---")
print(tfidf_matrix)
# Shape: (number of documents, number of unique terms in vocabulary)
print(f"\nShape of matrix: {tfidf_matrix.shape}")

# 3. Inspect the results
# Get the vocabulary (mapping from term to column index)
vocab = vectorizer.get_feature_names_out()
print(f"\nVocabulary ({len(vocab)} terms): {vocab}")

# Convert the sparse matrix to a dense DataFrame for easier viewing (only for small matrices!)
tfidf_df = pd.DataFrame(tfidf_matrix.toarray(), columns=vocab, index=[f"Doc {i+1}" for i in range(len(corpus))])
print("\n--- TF-IDF Matrix (DataFrame) ---")
# Displaying with higher precision
pd.set_option('display.precision', 3)
print(tfidf_df)

# Show IDF weights learned by the vectorizer
idf_weights = pd.Series(vectorizer.idf_, index=vocab)
print("\n--- Learned IDF Weights ---")
print(idf_weights.sort_values(ascending=False))

```

**Interpretation:**

*   The `tfidf_df` shows the TF-IDF score for each term (column) in each document (row).
*   Higher scores indicate terms that are more characteristic of a specific document relative to the corpus. For example, "corpora" has a high score only in Doc 2. "Scope" has a high score only in Doc 3.
*   Terms appearing in many documents (like "humanities", "analysis", "computing") have lower IDF weights (closer to 1.0 after smoothing/normalization in scikit-learn's calculation) and thus their TF-IDF scores are influenced more by their TF within each document.
*   The rows of the TF-IDF matrix are the **vector representations** of the documents.

**Applications in DH:**

*   **Document Similarity:** Calculate the cosine similarity between document vectors to find documents with similar content.
*   **Information Retrieval/Search:** Rank documents based on their relevance to a query vector.
*   **Keyword Extraction:** Identify the terms with the highest TF-IDF scores within a document as potential keywords.
*   **Feature Engineering:** Use TF-IDF vectors as input features for machine learning models (like classifiers or clustering algorithms, including topic modeling).

TF-IDF provides a more nuanced way to represent text than raw counts, capturing term importance and forming the basis for many advanced text analysis techniques.

➡️ **Next Step:** [[DH Advanced - 02 Introduction to Topic Modeling with LDA]]

---

*Back to [[Welcome to the DHP]] | [[DH Advanced - Start Here]]*