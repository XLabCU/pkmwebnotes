---
title: "02: Introduction to Topic Modeling with LDA"
created: 2025-08-05T12:57:24.739Z
tags: [advanced]
---
# DH Advanced - 02: Introduction to Topic Modeling with LDA

While TF-IDF helps identify important words within documents ([[DH Advanced - 01 Vector Space Models and TF-IDF]]), **Topic Modeling** aims to discover abstract "topics" that occur in a collection of documents. It's an unsupervised machine learning technique, meaning it finds patterns without pre-defined labels.

**Latent Dirichlet Allocation (LDA)** is a popular generative probabilistic model for topic modeling.

**Conceptual Idea of LDA:**

LDA assumes the following generative process for a corpus:

1.  For each document, choose a distribution over topics (e.g., Document 1 is 70% Topic A, 20% Topic B, 10% Topic C).
2.  For each word in that document:
    *   Choose a topic based on the document's topic distribution.
    *   Choose a word based on that chosen topic's distribution over words (e.g., Topic A is likely to generate words like "data", "analysis", "compute"; Topic B might generate "history", "archive", "source").

The goal of LDA algorithms is to work backward from the observed documents (the words) to infer the hidden (latent) structure: the topics (as word distributions) and the documents' topic distributions.

**Implementation with `gensim`:**

The `gensim` library is specifically designed for topic modeling and natural language processing tasks.

**Installation:**

If not already installed:
`pip install gensim nltk`
*(You might also need `matplotlib` for potential visualization later).*

**Setup and Data Preparation:**

LDA works best with reasonably clean, tokenized text. We need to:

1.  Tokenize documents.
2.  Remove stop words, punctuation, and possibly numbers.
3.  Lemmatize words (often preferred over stemming for LDA as it produces real words).
4.  Create a `gensim` Dictionary (word -> ID mapping).
5.  Create a `gensim` Corpus (document -> Bag-of-Words representation).

```python
import gensim
from gensim import corpora
from gensim.models import LdaModel
import nltk
from nltk.tokenize import word_tokenize
from nltk.corpus import stopwords
from nltk.stem import WordNetLemmatizer
import string
import re # For removing numbers

# --- Ensure NLTK resources are downloaded ---
# (Run these in a separate cell or ensure they were run previously)
try:
    nltk.data.find('corpora/wordnet')
    nltk.data.find('corpora/stopwords')
    nltk.data.find('tokenizers/punkt')
except LookupError:
    print("Downloading necessary NLTK data (wordnet, stopwords, punkt)...")
    nltk.download('wordnet', quiet=True)
    nltk.download('stopwords', quiet=True)
    nltk.download('punkt', quiet=True)
    print("Downloads complete.")

# --- Sample Corpus (Slightly larger) ---
doc1 = "Digital humanities blends computational methods with humanities research questions."
doc2 = "Text analysis and data visualization are common techniques in digital scholarship."
doc3 = "Large digital archives provide new sources for historical research and analysis."
doc4 = "Computational linguistics studies language structure using computing tools."
doc5 = "Network analysis helps visualize relationships in historical or literary texts."
doc6 = "Data mining large corpora can reveal patterns unseen through traditional reading."
doc7 = "Ethical considerations are crucial when using digital methods on cultural heritage data."
doc8 = "Scholars use topic modeling to explore themes in large collections of texts."

corpus_docs = [doc1, doc2, doc3, doc4, doc5, doc6, doc7, doc8]

# --- Text Preprocessing Function ---
lemmatizer = WordNetLemmatizer()
stop_words = set(stopwords.words('english'))
# Add custom words to ignore if needed
# stop_words.update(['digital', 'humanities', 'computational', 'methods'])

def preprocess_text(text):
    # Tokenize and lowercase
    tokens = word_tokenize(text.lower())
    # Remove punctuation and numbers, lemmatize, remove stop words
    processed_tokens = []
    for token in tokens:
        if token not in string.punctuation and not re.match(r'^\d+$', token): # Remove punctuation and numbers
            lemma = lemmatizer.lemmatize(token)
            if lemma not in stop_words and len(lemma) > 2: # Remove stop words and short tokens
                processed_tokens.append(lemma)
    return processed_tokens

# Preprocess all documents
processed_corpus = [preprocess_text(doc) for doc in corpus_docs]

print("--- Sample Preprocessed Document ---")
print(processed_corpus[0])

# --- Create Gensim Dictionary and Corpus ---
# Create Dictionary (maps words to unique integer IDs)
dictionary = corpora.Dictionary(processed_corpus)
# Filter extremes (optional but often good practice)
# no_below: Keep tokens present in at least X documents
# no_above: Keep tokens present in no more than Y% of documents
# keep_n: Keep only the top N most frequent tokens
dictionary.filter_extremes(no_below=2, no_above=0.8)

# Create Corpus (converts each document into Bag-of-Words format: list of (token_id, token_count))
bow_corpus = [dictionary.doc2bow(doc) for doc in processed_corpus]

print("\n--- Sample Dictionary Mapping (Word -> ID) ---")
print(list(dictionary.items())[:10]) # Show first 10 mappings

print("\n--- Sample Bag-of-Words Document Representation ---")
# Example: Document 1 BoW representation (token_id, count)
print(bow_corpus[0])
# Verify word mapping for the first document
print("Corresponding words:", [(dictionary[id], count) for id, count in bow_corpus[0]])

```

**Training the LDA Model:**

```python
# --- Train LDA Model ---
# Set number of topics (this often requires experimentation)
num_topics = 3 # Let's try 3 topics for this small corpus

# Build the LDA model
# Key parameters:
#   corpus: The Bag-of-Words corpus
#   id2word: The dictionary mapping IDs to words
#   num_topics: The desired number of topics
#   passes: Number of passes through the corpus during training
#   alpha, eta: Hyperparameters influencing topic/word distributions (often left as 'auto')
#   random_state: For reproducible results

print(f"\nTraining LDA model with {num_topics} topics...")
lda_model = LdaModel(corpus=bow_corpus,
                     id2word=dictionary,
                     num_topics=num_topics,
                     random_state=42, # For reproducibility
                     passes=10,      # Increase passes for potentially better results
                     alpha='auto',
                     eta='auto')
print("Training complete.")

# --- Interpreting the Results ---

# 1. Print the Topics (Keywords for each topic)
print(f"\n--- Top words for each of the {num_topics} topics ---")
# format_topics returns list of (topic_id, [(word, probability), ...])
# print_topics is a convenience method
topics = lda_model.print_topics(num_words=5) # Show top 5 words per topic
for topic in topics:
    print(topic)

# 2. Get Topic Distribution for a Specific Document
doc_id_to_check = 0
print(f"\n--- Topic distribution for Document {doc_id_to_check + 1} ---")
# Get the topic distribution for the first document in the BoW corpus
doc_lda = lda_model[bow_corpus[doc_id_to_check]]
print(f"Original Doc: {corpus_docs[doc_id_to_check]}")
print("Distribution (Topic ID, Probability):", doc_lda)

# Print with topic keywords for better interpretation
topic_names = {i: ", ".join([word for word, prob in lda_model.show_topic(i, topn=3)]) for i in range(num_topics)}
print("Interpreted Distribution:")
for topic_id, prob in doc_lda:
     print(f"  Topic {topic_id} ({topic_names[topic_id]}, ...): {prob:.3f}")

```

**Interpretation and Considerations:**

*   **Topics as Word Distributions:** Each topic is represented by the words most likely to be generated from it. Look at the top words to assign a human-interpretable label to the topic (e.g., "Topic 0 might relate to research methods/analysis", "Topic 1 to text/corpora", etc.). *Note: With small corpora, topics can be noisy or mixed.*
*   **Documents as Topic Distributions:** Each document is represented as a mixture of topics. This can be used for document clustering, similarity analysis based on topic profiles, or understanding the thematic composition of individual texts.
*   **Choosing `num_topics`:** This is a crucial and often tricky step. There's no single perfect method.
    *   **Trial and Error:** Train models with different numbers of topics and evaluate their interpretability.
    *   **Coherence Scores:** Metrics like `u_mass` or `c_v` measure how semantically coherent the words within each discovered topic are. Higher coherence is generally better. `gensim` provides tools to calculate these.
    *   **Perplexity:** A measure of how well the model predicts unseen data (lower is better), but doesn't always correlate well with human interpretability.
*   **Preprocessing Matters:** The quality of topics heavily depends on the text preprocessing steps (stop words, lemmatization, etc.).

**Applications in DH:**

*   **Thematic Analysis:** Discovering dominant themes in large collections of texts (e.g., novels, historical documents, journal articles, social media posts).
*   **Corpus Exploration:** Getting a high-level overview of the content structure of a large, unfamiliar dataset.
*   **Document Comparison/Clustering:** Grouping documents based on their thematic similarity.
*   **Tracking Topic Evolution:** Analyzing how topics change over time in temporally ordered corpora.

LDA provides a powerful lens for exploring the latent thematic structure within text collections, moving beyond simple keyword analysis.

➡️ **Next Step:** [[DH Advanced - 03 Named Entity Recognition (NER)]]

---

*Back to [[Welcome to the DHP]] | [[DH Advanced - Start Here]]*