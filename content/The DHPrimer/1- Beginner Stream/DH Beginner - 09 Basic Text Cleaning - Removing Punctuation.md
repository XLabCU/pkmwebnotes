---
title: "09: Basic Text Cleaning - Removing Punctuation"
created: 2025-08-05T12:57:24.739Z
tags: [beginner]
---
# DH Beginner - 09: Basic Text Cleaning - Removing Punctuation

In our simple word count ([[DH Beginner - 07 Simple Text Analysis - Word Count]]), we noticed that punctuation attached to words (like "file.") was treated as part of the word. For many analyses (like counting word frequencies), we want "file." and "file" to be counted as the same word. This requires **text cleaning**.

A basic cleaning step is removing punctuation.

**Method: Using `replace()`**

One straightforward way is to use the string method `.replace(old, new)`. It returns a *new* string where all occurrences of the `old` substring are replaced by the `new` substring.

We can chain multiple `.replace()` calls to remove various punctuation marks.

```python
# Example text
text = "Hello, world! This is text. Is it clean?"
print(f"Original: '{text}'")

# Remove comma
cleaned_text = text.replace(",", "")
print(f"No comma: '{cleaned_text}'")

# Remove comma AND period
cleaned_text = text.replace(",", "").replace(".", "")
print(f"No comma or period: '{cleaned_text}'")

# Remove comma, period, AND question mark
cleaned_text = text.replace(",", "").replace(".", "").replace("!", "").replace("?", "")
print(f"No common punctuation: '{cleaned_text}'")

# Important: Also convert to lowercase for consistency
cleaned_text = cleaned_text.lower()
print(f"Cleaned and lowercased: '{cleaned_text}'")
```

**Applying to our File:**

Let's combine file reading, lowercasing, punctuation removal, and splitting.

```python
import string # Import the string module to get a list of punctuation

filename = "sample2.txt"

try:
    # 1. Read the file content
    with open(filename, 'r', encoding='utf-8') as f:
        full_content = f.read()
    print(f"--- Original Text ---\n{full_content}\n")

    # 2. Convert to lowercase
    lower_content = full_content.lower()
    print(f"--- Lowercase Text ---\n{lower_content}\n")

    # 3. Remove punctuation
    # We can iterate through all standard punctuation marks
    cleaned_content = lower_content
    # string.punctuation gives us !"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~
    print(f"Removing punctuation: {string.punctuation}")
    for punc in string.punctuation:
        cleaned_content = cleaned_content.replace(punc, "")

    # It's also good to replace newline characters with spaces
    # if we want to treat the whole text as one sequence of words
    cleaned_content = cleaned_content.replace('\n', ' ')

    print(f"--- Cleaned Text ---\n{cleaned_content}\n")

    # 4. Split into words
    words = cleaned_content.split()
    print(f"--- List of Cleaned Words ---\n{words}\n")

    # 5. Count the words (compare with count from File 07)
    word_count = len(words)
    print(f"Total cleaned word count: {word_count}")

except FileNotFoundError:
    print(f"Error: The file '{filename}' was not found.")
    print("Please make sure 'sample.txt' exists (created in [[DH Beginner - 05 Creating a Sample Text File]]).")

```

**Explanation:**

*   `import string`: This line imports a built-in Python module that contains useful string constants, including `string.punctuation`.
*   We loop through each character in `string.punctuation`.
*   Inside the loop, `cleaned_content.replace(punc, "")` replaces the current punctuation mark (`punc`) with an empty string (effectively removing it). The result is reassigned back to `cleaned_content`.
*   We also explicitly replace the newline character `\n` with a space to avoid words at the end of a line joining with words at the start of the next when splitting.
*   Finally, we `.split()` the fully cleaned string.

Notice the list of words now doesn't include punctuation like periods. This cleaned list is much better for frequency analysis.

**Note:** This method is basic. It might remove hyphens in words like "well-being" or apostrophes in contractions like "don't". More advanced cleaning uses techniques like tokenization and stemming/lemmatization, often provided by libraries like NLTK or spaCy.

➡️ **Next Step:** [[10: Counting Word Frequencies|DH Beginner - 10 Counting Word Frequencies]]

---

_Back to [[Welcome to the DHP]] | [[DH Beginner - Start Here]]_