---
title: "07: Simple Text Analysis - Word Count"
created: 2025-08-05T12:57:24.739Z
tags: [beginner]
---

# DH Beginner - 07: Simple Text Analysis - Word Count

Let's perform our first simple text analysis: counting the number of words in our `sample.txt` file.

**Steps:**

1.  Read the file content into a string.
2.  (Optional but recommended) Convert the text to lowercase to treat "The" and "the" as the same word.
3.  Split the string into a list of individual words.
4.  Count the number of items in the list.

**Code:**

```python
filename = "sample2.txt"

try:
    # 1. Read the file content
    with open(filename, 'r', encoding='utf-8') as f:
        full_content = f.read()

    print(f"--- Original Text ---\n{full_content}\n")

    # 2. Convert to lowercase
    lower_content = full_content.lower()
    print(f"--- Lowercase Text ---\n{lower_content}\n")

    # 3. Split the string into words
    # The .split() method without arguments splits on whitespace (spaces, tabs, newlines)
    words = lower_content.split()
    print(f"--- List of Words ---\n{words}\n")

    # 4. Count the words
    word_count = len(words)
    print(f"Total word count: {word_count}")

except FileNotFoundError:
    print(f"Error: The file '{filename}' was not found.")
    print("Please make sure 'sample.txt' exists (created in [[DH Beginner - 05 Creating a Sample Text File]]).")
```

```codeResult [Execution #1] [Block #1] 
--- Original Text ---
This is the first line of our sample text file.
Digital Humanities often involves analyzing text.
This file provides a small example.
Let's count the words later.
End of file.


--- Lowercase Text ---
this is the first line of our sample text file.
digital humanities often involves analyzing text.
this file provides a small example.
let's count the words later.
end of file.


--- List of Words ---
['this', 'is', 'the', 'first', 'line', 'of', 'our', 'sample', 'text', 'file.', 'digital', 'humanities', 'often', 'involves', 'analyzing', 'text.', 'this', 'file', 'provides', 'a', 'small', 'example.', "let's", 'count', 'the', 'words', 'later.', 'end', 'of', 'file.']

Total word count: 30
```

**Explanation:**

* full\_content.lower(): This is a **string method** that returns a new string with all characters converted to lowercase.
* lower\_content.split(): This is another string method. When called without arguments, it splits the string wherever it finds whitespace (spaces, newlines \\n, tabs \\t) and returns a **list** of the resulting substrings (the words).
* len(words): We use the len() function on the words list to find out how many items (words) it contains.

**Limitations:**

This is a very basic word count. Notice that "file." is treated as a single word because .split() doesn't automatically handle punctuation attached to words. More advanced analysis would involve removing punctuation first.

**Congratulations!** You've read text from a file and performed a basic analysis using Python. This forms the foundation for many more complex DH tasks.

---

➡️ **Next Step:** [[08: Repeating Tasks with Loops (For Loop)|DH Beginner - 08 Repeating Tasks with Loops (For Loop)]]


_Back to [[Welcome to the DHP]] | [[DH Beginner - Start Here]]_