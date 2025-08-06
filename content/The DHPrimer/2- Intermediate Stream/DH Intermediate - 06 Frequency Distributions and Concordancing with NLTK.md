---
title: "06: Frequency Distributions and Concordancing with NLTK"
created: 2025-08-05T12:57:24.739Z
tags: [project]
---
# DH Intermediate - 06: Frequency Distributions and Concordancing with NLTK

Building on tokenization and stop word removal, NLTK provides convenient tools for common text analysis tasks like analyzing word frequencies and examining words in context (concordancing).

**NLTK's `FreqDist` and `Text` Objects:**

*   **`FreqDist`**: A specialized dictionary-like class for counting frequencies. It's optimized for frequency-related operations like finding the most common items.
*   **`Text`**: A wrapper class around a sequence of tokens (like a list of words) that provides several useful methods for textual analysis, including concordancing.

**Setup:**

Let's use a slightly longer sample text and apply the cleaning steps from the previous notebook.

```python
import nltk
from nltk.tokenize import word_tokenize
from nltk.corpus import stopwords
import string

# Sample text (longer)
text = """Project Gutenberg offers over 70,000 free eBooks. Choose among free epub and Kindle eBooks, download them or read them online. You will find the world's great literature here, with focus on older works for which U.S. copyright has expired. Thousands of volunteers digitized and diligently proofread the eBooks, for you to enjoy. This is a great resource for Digital Humanities projects analyzing literature. Analyzing text is fun!"""

# Ensure NLTK data is available (run download block in previous notes if needed)
try:
    stop_words = set(stopwords.words('english'))
    # Optional: Add custom stop words if needed
    # stop_words.update(['gutenberg', 'project'])
except LookupError:
    print("Stopwords not found. Please run the NLTK download code block first.")
    stop_words = set()

# --- Text Preparation ---
# 1. Tokenize
tokens = word_tokenize(text)

# 2. Lowercase
lower_tokens = [word.lower() for word in tokens]

# 3. Filter out punctuation and stop words
filtered_tokens = [
    word for word in lower_tokens
    if word.isalpha() and word not in stop_words
]

print(f"--- Original Text Snippet ---\n{text[:100]}...\n")
print(f"--- Filtered Tokens (Sample) ---\n{filtered_tokens[:20]}...\n")

```

**1. Frequency Distribution with `FreqDist`:**

We can feed our list of filtered tokens directly into `FreqDist`.

```python
from nltk.probability import FreqDist

# Create a Frequency Distribution from filtered tokens
fdist = FreqDist(filtered_tokens)

# --- Analyzing the Frequency Distribution ---

# Get the total number of tokens (samples) counted
print(f"Total filtered tokens counted: {fdist.N()}")

# Get the number of unique tokens (types)
print(f"Number of unique filtered words: {fdist.B()}") # B for bins/types

# Show the most common words
num_most_common = 10
print(f"\n--- Top {num_most_common} Most Common Filtered Words ---")
# .most_common(n) returns a list of (word, count) tuples
common_words = fdist.most_common(num_most_common)
for word, count in common_words:
    print(f"'{word}': {count}")

# Get the frequency of a specific word
specific_word = 'ebooks'
print(f"\nFrequency of '{specific_word}': {fdist[specific_word]}") # Access like a dictionary

# --- Plotting the Frequency Distribution ---
# Requires matplotlib
try:
    import matplotlib.pyplot as plt
    import seaborn as sns # Using seaborn style for aesthetics
    sns.set_style('darkgrid')

    plt.figure(figsize=(10, 6))
    fdist.plot(num_most_common, title=f'Top {num_most_common} Most Frequent Words')
    plt.show()
except ImportError:
    print("\nMatplotlib/Seaborn not installed. Skipping frequency plot.")
except Exception as e:
    print(f"\nError plotting frequency distribution: {e}")

```

`FreqDist` gives us a quick way to see which content words are most prominent after removing common stop words and punctuation.

**2. Concordancing with `nltk.Text`:**

A concordance shows every occurrence of a given word, together with some surrounding context (KWIC - Key Word In Context). This is useful for understanding how a word is used. We typically create an `nltk.Text` object from the *original* (or less heavily filtered) list of tokens to preserve the context.

```python
# Create an NLTK Text object from the original, lowercased tokens
# (We use lower_tokens to make searching case-insensitive, but keep stop words for context)
nltk_text = nltk.Text(lower_tokens)

# --- Using .concordance() ---
search_word = 'ebooks'
print(f"\n--- Concordance for '{search_word}' ---")
# concordance() prints directly, doesn't return a list
# It might wrap lines depending on your display width
nltk_text.concordance(search_word, width=80, lines=10) # width=characters, lines=max lines

search_word_2 = 'literature'
print(f"\n--- Concordance for '{search_word_2}' ---")
nltk_text.concordance(search_word_2, width=80, lines=5)

# --- Other useful nltk.Text methods ---
# .similar(): Find words appearing in similar contexts
# (Effectiveness varies with text size and content)
print(f"\n--- Words similar to '{search_word}' (contextually) ---")
nltk_text.similar(search_word, num=10)

# .collocations(): Find common bigrams (pairs of words)
# Might require larger text to find interesting collocations
print("\n--- Common Collocations (Bigrams) ---")
# collocations() prints directly
nltk_text.collocations(num=10, window_size=2)

```

**Explanation:**

*   `FreqDist(tokens)`: Creates the frequency distribution object.
*   `fdist.N()`: Total number of samples (tokens processed).
*   `fdist.B()`: Total number of unique types (unique words).
*   `fdist.most_common(n)`: Returns the `n` most frequent (word, count) pairs.
*   `fdist.plot(n)`: A built-in method (using Matplotlib) to quickly visualize the top `n` frequencies.
*   `nltk.Text(tokens)`: Creates the Text object. Best used with tokens that retain original context.
*   `nltk_text.concordance(word, width, lines)`: Prints the KWIC view for the specified `word`.
*   `nltk_text.similar(word)`: Finds words that share contexts with the target word (using statistical association measures).
*   `nltk_text.collocations()`: Identifies frequent word pairs (bigrams) based on statistical significance (Pointwise Mutual Information by default).

Frequency distributions and concordances are powerful exploratory tools in DH, allowing you to quickly grasp key themes and investigate word usage patterns within your texts.

➡️ **Next Step:** [[DH Intermediate - 07 Stemming and Lemmatization with NLTK]]

---

*Back to [[Welcome to the DHP]] | [[DH Intermediate - Start Here]]*