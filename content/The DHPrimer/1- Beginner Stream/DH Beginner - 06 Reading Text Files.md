---
title: "06: Reading Text Files"
created: 2025-08-05T12:57:24.739Z
tags: [beginner]
---

# DH Beginner - 06: Reading Text Files

One of the most common tasks in Digital Humanities is reading text from files (like `.txt`, `.csv`, `.xml`). Let's read the `sample.txt` file we created in the previous step.

**Reading a File:**

We use the `open()` function with mode `'r'` for "read".

*   `open('filename.txt', 'r')`: Opens the file for reading. This is the default mode if you don't specify one.
*   `file.read()`: Reads the *entire* content of the file into a single string.
*   `file.readlines()`: Reads the file line by line and returns a *list* of strings, where each string is one line (including the newline character `\n` at the end).
*   `file.readline()`: Reads just the *next* line from the file.

Again, using a `with` statement is recommended for automatically handling file closing.

**Example 1: Reading the whole file**

```python
filename = "sample.txt"

# Make sure you ran the code in the previous note ([[DH Beginner - 05 Creating a Sample Text File]])
# to create sample.txt first!

try:
    # Use 'with open' to read the file
    # 'r' mode means read
    # encoding='utf-8' is important for consistency
    with open(filename, 'r', encoding='utf-8') as f:
        full_content = f.read() # Read everything into one string

    print("--- Content read with file.read() ---")
    print(full_content)
    print(f"Type of full_content: {type(full_content)}")

except FileNotFoundError:
    print(f"Error: The file '{filename}' was not found.")
    print("Please go back and run the code in '[[DH Beginner - 05 Creating a Sample Text File]]'.")
```

```codeResult [Execution #1] [Block #1] 
--- Content read with file.read() ---
This is the first line of our sample text file.
Digital Humanities often involves analyzing text.
This file provides a small example.
Let's count the words later.
End of file.

Type of full_content: <class 'str'>
```

**Example 2: Reading line by line**

```python
filename = "sample2.txt"

try:
    with open(filename, 'r', encoding='utf-8') as f:
        lines_list = f.readlines() # Read all lines into a list

    print("\n--- Content read with file.readlines() ---")
    print(lines_list) # Notice the '\n' (newline character) at the end of most lines
    print(f"Type of lines_list: {type(lines_list)}")
    print(f"Number of lines: {len(lines_list)}")

    # You can access individual lines like any list item
    print(f"\nThe first line is: {lines_list[0]}")
    print(f"The second line is: {lines_list[1]}")

except FileNotFoundError:
    print(f"Error: The file '{filename}' was not found.")
    print("Please go back and run the code in '[[DH Beginner - 05 Creating a Sample Text File]]'.")
```

```codeResult [Execution #1] [Block #2] 
--- Content read with file.readlines() ---
['This is the first line of our sample text file.\n', 'Digital Humanities often involves analyzing text.\n', 'This file provides a small example.\n', "Let's count the words later.\n", 'End of file.\n']
Type of lines_list: <class 'list'>
Number of lines: 5

The first line is: This is the first line of our sample text file.

The second line is: Digital Humanities often involves analyzing text.
```

Now that we can get text data _into_ Python, we can start analyzing it.

➡️ **Next Step:** [[07: Simple Text Analysis - Word Count|DH Beginner - 07 Simple Text Analysis - Word Count]]

---

_Back to [[Welcome to the DHP]] | [[DH Beginner - Start Here]]_
