---
title: "10: Counting Word Frequencies"
created: 2025-08-05T12:57:24.739Z
tags: [beginner]
---
# DH Beginner - 10: Counting Word Frequencies

Now that we know how to loop and how to clean text, we can combine these skills to count the frequency of each unique word in our sample text. This is a fundamental task in text analysis.

We don't just want the total word count; we want to know *how many times* each specific word appears (e.g., "text" appears 3 times, "file" appears 2 times).

**Data Structure: Dictionaries (`dict`)**

A list isn't the best tool for storing frequencies because we need to associate each *word* (a unique key) with its *count* (a value). The perfect Python data structure for this is a **dictionary**.

Dictionaries store key-value pairs, enclosed in curly braces `{}`.

```python
# Example dictionary
student_grades = {
    "Alice": 85,
    "Bob": 92,
    "Charlie": 78
}

# Access a value using its key
print(f"Bob's grade: {student_grades['Bob']}") # Output: Bob's grade: 92

# Add a new entry
student_grades["David"] = 88
print(student_grades)

# Check if a key exists
print(f"Is Alice in the dictionary? {'Alice' in student_grades}") # Output: True
print(f"Is Eve in the dictionary? {'Eve' in student_grades}")   # Output: False
```

**Algorithm for Counting Frequencies:**

1.  Read and clean the text (lowercase, remove punctuation).
2.  Split the cleaned text into a list of words.
3.  Create an empty dictionary to store word counts (e.g., `word_counts = {}`).
4.  Loop through the list of words.
5.  For each `word`:
    *   Check if the `word` is already a key in the `word_counts` dictionary.
    *   If YES: Increment the count associated with that word (`word_counts[word] = word_counts[word] + 1` or shorthand `word_counts[word] += 1`).
    *   If NO: Add the `word` to the dictionary as a new key with a value (count) of 1 (`word_counts[word] = 1`).
6.  After the loop finishes, the dictionary will contain all unique words and their frequencies.

**Code:**

```python
import string

filename = "sample2.txt"
word_counts = {} # 3. Initialize an empty dictionary

try:
    # 1. Read and clean the text
    with open(filename, 'r', encoding='utf-8') as f:
        full_content = f.read()

    lower_content = full_content.lower()

    cleaned_content = lower_content
    for punc in string.punctuation:
        cleaned_content = cleaned_content.replace(punc, "")
    cleaned_content = cleaned_content.replace('\n', ' ')

    # 2. Split into words
    words = cleaned_content.split()
    print(f"--- Cleaned words ---\n{words}\n") # Show the words we are counting

    # 4. Loop through the words
    for word in words:
        if word: # Make sure the word is not an empty string (can happen with split)
            # 5. Check and update dictionary
            if word in word_counts:
                word_counts[word] += 1 # Increment count if word exists
            else:
                word_counts[word] = 1 # Add word with count 1 if new

    # 6. Display the results
    print(f"--- Word Frequencies ---\n{word_counts}")

    # Optional: Print frequencies more nicely
    print("\n--- Frequencies (Formatted) ---")
    # .items() gives key-value pairs
    for word, count in word_counts.items():
        print(f"'{word}': {count}")


except FileNotFoundError:
    print(f"Error: The file '{filename}' was not found.")
    print("Please make sure 'sample.txt' exists (created in [[DH Beginner - 05 Creating a Sample Text File]]).")

```

Run the code. The output will show the dictionary containing each unique (cleaned, lowercased) word from `sample.txt` and how many times it appeared.

**Congratulations!** You've combined file reading, loops, basic cleaning, and dictionaries to perform a common and useful DH task: calculating word frequencies.

**Further Steps:**

From here, you could:

*   Sort the frequencies to find the most common words.
*   Exclude common "stop words" (like "the", "is", "a") that don't carry much meaning.
*   Visualize the frequencies using charts.
*   Apply these techniques to larger texts!

➡️ **Next Step:** [[11: Wrap-up and Next Steps| DH Beginner - 11 Wrap-up and Next Steps]]