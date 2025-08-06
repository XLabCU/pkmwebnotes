---
title: "07: Stemming and Lemmatization with NLTK"
created: 2025-08-05T12:57:24.739Z
tags: [project]
---
# DH Intermediate - 07: Stemming and Lemmatization with NLTK

When analyzing word frequencies or topics, we often encounter different forms of the same word (e.g., "analyze", "analyzing", "analyzed", "analysis"). Treating these as separate words can dilute their overall importance. **Stemming** and **Lemmatization** are two techniques used to reduce words to a common base or root form, helping to group related terms.

**1. Stemming:**

Stemming algorithms work by crudely chopping off word endings (suffixes) based on predefined rules, aiming to reduce inflectional forms to a common stem.

*   **Pros:** Computationally fast and simple.
*   **Cons:** The resulting "stems" are often not actual dictionary words (e.g., "analysis" might become "analysi"). Can be overly aggressive (over-stemming, merging unrelated words) or not aggressive enough (under-stemming, failing to merge related words).
*   **Common Algorithm:** Porter Stemmer (older, widely known), Lancaster Stemmer (more aggressive), Snowball Stemmer (supports multiple languages, often preferred over Porter).

**Example using PorterStemmer:**

```python
import nltk
from nltk.stem import PorterStemmer
from nltk.tokenize import word_tokenize

# Initialize the stemmer
porter = PorterStemmer()

words = ["analyze", "analyzing", "analyzed", "analysis", "analyses", "computation", "computational", "compute", "computer", "computers", "universal", "university", "universe"]

print("--- Stemming Example (Porter) ---")
print(f"{'Original':<15} -> {'Stemmed':<15}")
print("-" * 30)
for word in words:
    stemmed_word = porter.stem(word)
    print(f"{word:<15} -> {stemmed_word:<15}")

# Apply to our previous filtered tokens
# (Using filtered_tokens from the previous notebook's code block)
# Make sure to run the setup block from DH Int 06 if needed
if 'filtered_tokens' in locals():
    stems = [porter.stem(token) for token in filtered_tokens]
    print("\n--- Stems from Sample Text (First 20) ---")
    print(stems[:20])

    # Compare frequency distribution
    from nltk.probability import FreqDist
    fdist_stems = FreqDist(stems)
    print("\n--- Top 5 Stem Frequencies ---")
    print(fdist_stems.most_common(5))
else:
    print("\nVariable 'filtered_tokens' not found. Run previous notebook setup.")

```

Notice how related words are often reduced to the same stem (e.g., "comput" for compute-related words), but the stems themselves aren't always dictionary words.

**2. Lemmatization:**

Lemmatization aims to reduce words to their base or dictionary form, known as the **lemma**. It uses vocabulary lookups and morphological analysis (considering the word's structure and context).

*   **Pros:** Produces actual dictionary words, generally more linguistically accurate than stemming.
*   **Cons:** Slower than stemming, requires more resources (like WordNet, a large lexical database included with NLTK). Performance often depends heavily on knowing the word's Part-of-Speech (POS) tag (noun, verb, adjective, etc.).
*   **Common Tool:** NLTK's `WordNetLemmatizer`.

**Example using WordNetLemmatizer:**

```python
import nltk
from nltk.stem import WordNetLemmatizer
from nltk.corpus import wordnet # Needed for POS tagging mapping

# Download wordnet if you haven't already
try:
    nltk.data.find('corpora/wordnet')
except nltk.downloader.DownloadError:
    print("Downloading NLTK data ('wordnet')...")
    nltk.download('wordnet', quiet=True)
    print("Download complete.")
except LookupError: # Handle case where it might already be downloaded but not found initially
     print("Downloading NLTK data ('wordnet')...")
     nltk.download('wordnet', quiet=True)
     print("Download complete.")


# Initialize the lemmatizer
lemmatizer = WordNetLemmatizer()

words = ["analyze", "analyzing", "analyzed", "analysis", "analyses", "computation", "computational", "compute", "computer", "computers", "universal", "university", "universe", "studies", "studying", "better", "best"]

print("\n--- Lemmatization Example (WordNet) ---")
print(f"{'Original':<15} -> {'Lemma (Default=Noun)':<15} -> {'Lemma (Verb)':<15} -> {'Lemma (Adjective)':<15}")
print("-" * 60)
for word in words:
    # Default POS is noun ('n')
    lemma_n = lemmatizer.lemmatize(word, pos='n')
    # Specify verb ('v')
    lemma_v = lemmatizer.lemmatize(word, pos='v')
    # Specify adjective ('a')
    lemma_a = lemmatizer.lemmatize(word, pos='a')
    print(f"{word:<15} -> {lemma_n:<20} -> {lemma_v:<15} -> {lemma_a:<15}")

# Applying to text requires POS tagging for accuracy (more complex, basic example below)
# Without POS tags, it defaults to noun and might not lemmatize verbs/adjectives correctly.
if 'filtered_tokens' in locals():
    lemmas_noun_default = [lemmatizer.lemmatize(token) for token in filtered_tokens] # Default POS = noun
    print("\n--- Lemmas from Sample Text (Default=Noun, First 20) ---")
    print(lemmas_noun_default[:20])

    # Compare frequency distribution
    fdist_lemmas = FreqDist(lemmas_noun_default)
    print("\n--- Top 5 Lemma (Default=Noun) Frequencies ---")
    print(fdist_lemmas.most_common(5))

    # NOTE: Accurate lemmatization requires Part-of-Speech tagging first.
    # We will cover POS tagging in a later notebook. For now, be aware
    # that lemmatizing without POS tags might not correctly handle verbs
    # (e.g., 'analyzing' might stay 'analyzing' instead of becoming 'analyze').
else:
    print("\nVariable 'filtered_tokens' not found. Run previous notebook setup.")


```

Observe how lemmatization produces dictionary words. Crucially, notice how the result changes based on the `pos` argument (`lemmatize(word, pos=...)`). "analyzing" becomes "analyze" only when specified as a verb (`pos='v'`). This highlights the importance of Part-of-Speech tagging for effective lemmatization.

**Stemming vs. Lemmatization:**

*   Use **Stemming** when speed is critical and you don't necessarily need linguistically correct base forms (e.g., some information retrieval tasks, large-scale text indexing where slight inaccuracies are acceptable).
*   Use **Lemmatization** when you need actual dictionary words and higher accuracy (e.g., linguistic analysis, chatbot input processing, information extraction where meaning is crucial). Be prepared to implement Part-of-Speech tagging for best results.

Both techniques help normalize text for analysis, reducing the feature space and grouping related terms, which is often beneficial for subsequent steps like topic modeling or document comparison.

➡️ **Next Step:** [[DH Intermediate - 08 Part-of-Speech Tagging with NLTK]] *(Essential for improving Lemmatization)* or potentially shifting to another topic like APIs/JSON if desired.

---

*Back to [[Welcome to the DHP]] | [[DH Intermediate - Start Here]]*