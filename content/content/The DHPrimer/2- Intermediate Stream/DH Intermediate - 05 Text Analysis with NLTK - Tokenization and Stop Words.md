---
title: "05: Text Analysis with NLTK - Tokenization and Stop Words"
created: 2025-08-05T12:57:24.739Z
tags: [project]
---
# DH Intermediate - 05: Text Analysis with NLTK - Tokenization and Stop Words

In the Beginner Strand, we performed basic text cleaning and word counting using simple string methods (`.lower()`, `.replace()`, `.split()`). For more robust and linguistically aware text processing, we turn to specialized libraries.

**NLTK (Natural Language Toolkit)** is a comprehensive Python library for various Natural Language Processing (NLP) tasks. It provides tools for classification, tokenization, stemming, tagging, parsing, and semantic reasoning.

**Installation:**

1.  Install the library: `pip install nltk`
2.  Download NLTK data: NLTK relies on external data packages (corpora, models). You need to download them after installation. Run the following Python code *once* (it might open a download window, or you can specify packages):

```python
# Run this block ONCE to download necessary NLTK data
import nltk

try:
    print("Downloading NLTK data ('punkt' and 'stopwords')...")
    nltk.download('punkt', quiet=True) # For tokenization
    nltk.download('stopwords', quiet=True) # For stop word lists
    print("NLTK data downloaded successfully (or already present).")
except Exception as e:
    print(f"Error downloading NLTK data: {e}")
    print("You might need to run this code in an environment with internet access.")
    print("Alternatively, run 'import nltk; nltk.download()' in a Python console for an interactive downloader.")

```

**1. Tokenization:**

Tokenization is the process of splitting text into meaningful units, called **tokens**. This is often more sophisticated than simple `.split()` because it can handle punctuation better (e.g., separating "don't" into "do" and "n't", or treating "U.S.A." as a single token, depending on the tokenizer). NLTK provides several tokenizers. `word_tokenize` is common.

```python
import nltk
from nltk.tokenize import word_tokenize

text = "Doesn't NLTK handle tokenization better? Yes, like U.S.A.!"

# Basic split (for comparison)
basic_split = text.lower().split()
print(f"Basic split:\n{basic_split}\n")

# NLTK word tokenization
# It often works better on the original case, then lowercase later if needed
tokens = word_tokenize(text)
print(f"NLTK word_tokenize:\n{tokens}\n")

# Often you'll lowercase *after* tokenization
lower_tokens = [word.lower() for word in tokens]
print(f"NLTK lowercased tokens:\n{lower_tokens}\n")
```

Notice how `word_tokenize` separates "Doesn't" into "Does" and "n't", and keeps punctuation like "?" and "!" as separate tokens. This finer granularity can be useful for analysis.

**2. Removing Stop Words:**

**Stop words** are common words (like "the", "a", "is", "in", "on") that often don't carry significant semantic weight for tasks like topic modeling or frequency analysis. Removing them can help focus on more meaningful words. NLTK provides standard stop word lists for various languages.

```python
import nltk
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize
import string # To remove punctuation tokens

# Ensure stopwords are available (run download block if needed)
try:
    stop_words = set(stopwords.words('english')) # Use a set for faster lookups
    # print(f"English stop words (sample): {list(stop_words)[:15]}")
except LookupError:
    print("Stopwords not found. Please run the NLTK download code block first.")
    stop_words = set() # Use empty set to avoid errors later

text = """This is a sample sentence, showing off the stop words filtration.
Digital Humanities uses tools like NLTK for analysis."""

# 1. Tokenize
tokens = word_tokenize(text)

# 2. Convert to lowercase
lower_tokens = [word.lower() for word in tokens]

# 3. Remove punctuation tokens AND stop words
filtered_tokens = []
for word in lower_tokens:
    if word.isalpha() and word not in stop_words: # Check if alphabetic AND not a stop word
        filtered_tokens.append(word)
    # Alternative check if keeping punctuation separate is desired:
    # if word not in stop_words and word not in string.punctuation:
    #    filtered_tokens.append(word)

print(f"\nOriginal text:\n{text}")
print(f"\nTokens (lowercase):\n{lower_tokens}")
print(f"\nTokens after removing stop words and punctuation:\n{filtered_tokens}")

# We can now perform frequency analysis on these filtered tokens
from collections import Counter
word_freq = Counter(filtered_tokens)
print("\n--- Word Frequencies (Filtered) ---")
print(word_freq.most_common(5)) # Show 5 most common

```

**Explanation:**

*   `nltk.download(...)`: Gets required resources.
*   `word_tokenize(text)`: Splits text into word/punctuation tokens.
*   `stopwords.words('english')`: Loads the English stop word list. We convert it to a `set` for efficient checking (`word in stop_words`).
*   We loop through the lowercased tokens.
*   `word.isalpha()`: Checks if the token consists only of alphabetic characters (a simple way to remove punctuation tokens like ',', '.', '?').
*   `word not in stop_words`: Checks if the word is in our stop word set.
*   If a token passes both checks, it's added to `filtered_tokens`.
*   `collections.Counter`: A convenient way to count frequencies (works like our manual dictionary method but is often faster).

Using NLTK for tokenization and stop word removal provides a more standardized and linguistically informed approach to text preparation compared to basic string manipulation, setting the stage for more advanced NLP tasks.

➡️ **Next Step:** [[DH Intermediate - 06 Frequency Distributions and Concordancing with NLTK]]

---

*Back to [[Welcome to the DHP]] | [[DH Intermediate - Start Here]]*