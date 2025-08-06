---
title: "05: Creating a Sample Text File"
created: 2025-08-05T12:57:24.739Z
tags: [beginner]
---

# DH Beginner - 05: Creating a Sample Text File

To practice reading and analyzing text, we first need a text file! We can actually use Python itself to create a simple one.

**Writing to a File:**

We use the `open()` function again, but this time with the mode `'w'` for "write".

*   `open('filename.txt', 'w')`: Opens the file for writing. **Warning:** If the file already exists, this will overwrite it completely!
*   `file.write("text")`: Writes the given string to the file.
*   `file.close()`: Closes the file, saving the changes.

It's good practice to use a `with` statement, which automatically closes the file for you, even if errors occur.

```python
# Define the text we want to write
text_content = """This is the first line of our sample text file.
Digital Humanities often involves analyzing text.
This file provides a small example.
Let's count the words later.
End of file.
"""

# Define the filename
filename = "sample2.txt"

# Use 'with open' to create and write to the file
# 'w' mode means write (will create or overwrite)
# encoding='utf-8' is good practice for text files
with open(filename, 'w', encoding='utf-8') as f:
    f.write(text_content)

print(f"File '{filename}' created successfully.")
print("You might need to refresh your file browser in this app (if available) to see it.")
```

```codeResult [Execution #1] [Block #1] 
File 'sample2.txt' created successfully.
You might need to refresh your file browser in this app (if available) to see it.
```

```codeResult [Execution #1] [Block #1] 
File 'sample.txt' created successfully.
You might need to refresh your file browser in this app (if available) to see it.
```

Run the code block above. This will create a file named sample.txt in the same directory where this notebook is running. It contains the multi-line text defined in the text\_content variable. **march 31st** modify the code so that it puts it into that directory and point that out; not clear where this will save in the packaged version! #todo

Now that we have a file, we can learn how to read its contents back into Python.

➡️ **Next Step:** [[06: Reading Text Files|DH Beginner - 06 Reading Text Files]]

_Back to [[Welcome to the DHP]] | [[DH Beginner - Start Here]]_