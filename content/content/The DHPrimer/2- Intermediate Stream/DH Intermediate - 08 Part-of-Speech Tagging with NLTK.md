---
title: "08: Part-of-Speech Tagging with NLTK"
created: 2025-08-05T12:57:24.739Z
tags: [project]
---
# DH Intermediate - 08: Part-of-Speech Tagging with NLTK

As we saw with lemmatization ([[DH Intermediate - 07 Stemming and Lemmatization with NLTK]]), knowing a word's grammatical category or **Part-of-Speech (POS)** – whether it's a noun, verb, adjective, adverb, etc. – is crucial for accurate linguistic analysis. **POS tagging** is the process of assigning a POS tag to each token in a text.

NLTK provides convenient functions for POS tagging, typically using pre-trained statistical models.

**Setup and Data:**

*   NLTK's default POS tagger relies on the 'averaged_perceptron_tagger'. You might need to download it if you haven't before.

```python
import nltk

# Download tagger resources if needed
try:
    nltk.data.find('taggers/averaged_perceptron_tagger')
    print("Averaged Perceptron Tagger found.")
except LookupError:
    print("Downloading NLTK data ('averaged_perceptron_tagger')...")
    nltk.download('averaged_perceptron_tagger', quiet=True)
    print("Download complete.")

# We also need the 'punkt' tokenizer data
try:
    nltk.data.find('tokenizers/punkt')
    print("Punkt tokenizer found.")
except LookupError:
    print("Downloading NLTK data ('punkt')...")
    nltk.download('punkt', quiet=True)
    print("Download complete.")

```

**Performing POS Tagging:**

The primary function is `nltk.pos_tag()`, which takes a list of tokens as input.

```python
import nltk
from nltk.tokenize import word_tokenize

text = "They refuse to permit us to obtain the refuse permit." # Example with ambiguous words

# 1. Tokenize the text
tokens = word_tokenize(text)
print(f"Tokens: {tokens}\n")

# 2. Perform POS tagging
tagged_tokens = nltk.pos_tag(tokens)

print("--- POS Tagged Tokens ---")
print(tagged_tokens)
```

**Understanding the Tags:**

NLTK uses tags from the Penn Treebank Tagset. Here are some common ones:

*   `NN`: Noun, singular or mass (e.g., *dog, book, refuse*)
*   `NNS`: Noun, plural (e.g., *dogs, books*)
*   `NNP`: Proper noun, singular (e.g., *Jane, London*)
*   `NNPS`: Proper noun, plural (e.g., *Austens, Smiths*)
*   `VB`: Verb, base form (e.g., *take, refuse*)
*   `VBD`: Verb, past tense (e.g., *took, refused*)
*   `VBG`: Verb, gerund or present participle (e.g., *taking, refusing*)
*   `VBN`: Verb, past participle (e.g., *taken, refused*)
*   `VBP`: Verb, non-3rd person singular present (e.g., *take, refuse*)
*   `VBZ`: Verb, 3rd person singular present (e.g., *takes, refuses*)
*   `JJ`: Adjective (e.g., *happy, large*)
*   `JJR`: Adjective, comparative (e.g., *happier, larger*)
*   `JJS`: Adjective, superlative (e.g., *happiest, largest*)
*   `RB`: Adverb (e.g., *quickly, very*)
*   `IN`: Preposition or subordinating conjunction (e.g., *in, of, on, while*)
*   `DT`: Determiner (e.g., *the, a, an, some*)
*   `PRP`: Personal pronoun (e.g., *I, he, she, they*)
*   `PRP$`: Possessive pronoun (e.g., *my, his, her, their*)
*   `.` : Punctuation marks

You can get details on a tag using `nltk.help.upenn_tagset('TAG')`:

```python
# Get help on specific tags
#nltk.help.upenn_tagset('VBZ')
#nltk.help.upenn_tagset('NN')
#nltk.help.upenn_tagset('PRP')
```

Notice in the example above how "refuse" is correctly tagged as a verb (`VBP`) in the first instance and a noun (`NN`) in the second. This context-awareness is key.

**Improving Lemmatization with POS Tags:**

Now we can revisit lemmatization and provide the correct POS tag (translated into the format WordNetLemmatizer expects) for better accuracy.

```python
from nltk.stem import WordNetLemmatizer
from nltk.corpus import wordnet # For tag mapping

lemmatizer = WordNetLemmatizer()

# Function to map Penn Treebank tags to WordNet tags
def get_wordnet_pos(treebank_tag):
    if treebank_tag.startswith('J'):
        return wordnet.ADJ
    elif treebank_tag.startswith('V'):
        return wordnet.VERB
    elif treebank_tag.startswith('N'):
        return wordnet.NOUN
    elif treebank_tag.startswith('R'):
        return wordnet.ADV
    else:
        # Default to noun if no mapping found
        return wordnet.NOUN

# Example sentence
text_to_lemmatize = "The striped bats are hanging upside down watching their reflections."
tokens_to_lemmatize = word_tokenize(text_to_lemmatize)
tagged_lem_tokens = nltk.pos_tag(tokens_to_lemmatize)

print(f"\n--- Original Tagged Tokens ---\n{tagged_lem_tokens}\n")

print("--- Lemmatization with POS Tagging ---")
print(f"{'Original':<15} -> {'POS Tag':<10} -> {'WordNet POS':<12} -> {'Lemma':<15}")
print("-" * 60)

lemmas_with_pos = []
for word, tag in tagged_lem_tokens:
    wn_tag = get_wordnet_pos(tag)
    lemma = lemmatizer.lemmatize(word, pos=wn_tag)
    lemmas_with_pos.append(lemma)
    print(f"{word:<15} -> {tag:<10} -> {wn_tag:<12} -> {lemma:<15}")

print(f"\nLemmatized sentence: {' '.join(lemmas_with_pos)}")

# Compare to default (noun) lemmatization
lemmas_noun_default = [lemmatizer.lemmatize(word) for word, tag in tagged_lem_tokens]
print(f"Default Noun Lemmas: {lemmas_noun_default}")

```

Note how "striped" (adjective), "are hanging" (verb), and "watching" (verb) are correctly lemmatized to "strip", "be hang", and "watch" when using the POS tag information, whereas the default noun-based lemmatization fails on some of these. (Note: "be" is the lemma for forms of "to be").

**Applications in DH:**

*   **Improved Text Normalization:** For more accurate frequency counts, topic modeling, etc.
*   **Syntactic Analysis:** Understanding sentence structure.
*   **Information Extraction:** Identifying nouns (potential entities), verbs (actions), adjectives (descriptions).
*   **Stylometry:** Analyzing authors' use of different word classes.

POS tagging is a fundamental step in many NLP pipelines, enabling more sophisticated and accurate analysis of textual data.

➡️ **Next Step:** [[DH Intermediate - 09 Introduction to APIs and JSON]]

---

*Back to [[Welcome to the DHP]] | [[DH Intermediate - Start Here]]*