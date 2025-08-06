---
title: "08: Repeating Tasks with Loops (For Loop)"
created: 2025-08-05T12:57:24.739Z
tags: [beginner]
---
# DH Beginner - 08: Repeating Tasks with Loops (For Loop)

Imagine you have a list of items (like our book titles) and you want to perform the same action on each item (like printing each title). Doing it manually for each item would be tedious and error-prone, especially for long lists. This is where **loops** come in!

The most common type of loop in Python for iterating over a sequence (like a list or the lines from a file) is the **`for` loop**.

**Basic Syntax:**

```python
for variable_name in sequence:
    # Code to execute for each item in the sequence
    # This block is indented (usually 4 spaces)
    print(variable_name) # Example action
```

*   `for`: Keyword starting the loop.
*   `variable_name`: A temporary variable that will hold each item from the sequence, one by one, during each pass (iteration) of the loop. You choose this name (e.g., `item`, `book`, `line`, `word`).
*   `in`: Keyword separating the temporary variable from the sequence.
*   `sequence`: The list, string, or other iterable object you want to loop through.
*   `:`: Colon marks the end of the `for` statement.
*   **Indented block:** The code that runs *inside* the loop. Indentation is crucial in Python; it defines the loop's body.

**Example 1: Looping through a List**

Let's revisit our list of novels:

```python
austen_novels = ["Sense and Sensibility", "Pride and Prejudice", "Mansfield Park", "Emma"]

print("--- Printing each novel ---")
for novel in austen_novels:
    print(f"Processing: {novel}")

print("--- Loop finished ---")
```

```codeResult [Exec #1] [Block #2] 
--- Printing each novel ---
Processing: Sense and Sensibility
Processing: Pride and Prejudice
Processing: Mansfield Park
Processing: Emma
--- Loop finished ---
```

Run this. You'll see each novel printed on a new line, prefixed with "Processing: ".

**Example 2: Looping through Lines from a File**

Remember `readlines()` gave us a list of strings (lines)? We can loop through that!

```python
filename = "sample2.txt" # Assuming this file exists from previous steps

try:
    with open(filename, 'r', encoding='utf-8') as f:
        lines_list = f.readlines()

    print(f"\n--- Processing {filename} line by line ---")
    line_number = 1
    for line in lines_list:
        # .strip() removes leading/trailing whitespace, including the newline '\n'
        print(f"Line {line_number}: {line.strip()}")
        line_number = line_number + 1 # Increment the line number

except FileNotFoundError:
    print(f"Error: The file '{filename}' was not found.")
    print("Please make sure 'sample.txt' exists (created in [[DH Beginner - 05 Creating a Sample Text File]]).")

```

**Example 3: Looping through Words**

We can also loop through the list of words we got using `.split()`:

```python
sample_text = "This is a sample text."
words = sample_text.lower().split() # Lowercase and split into words

print("\n--- Processing words ---")
for word in words:
    print(f"Word: {word}")
```

Loops are fundamental for automating repetitive tasks on collections of data, a cornerstone of computational analysis in DH.

➡️ **Next Step:** [[09: Basic Text Cleaning - Removing Punctuation|DH Beginner - 09 Basic Text Cleaning - Removing Punctuation]]

---

_Back to [[Welcome to the DHP]] | [[DH Beginner - Start Here]]_