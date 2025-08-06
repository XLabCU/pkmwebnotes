---
title: "03: Variables and Data Types"
created: 2025-08-05T12:57:24.739Z
tags: [beginner]
---

# DH Beginner - 03: Variables and Data Types

Imagine you want to store a piece of information to use later. Instead of typing it out every time, you can assign it a name. This named storage location is called a **variable**.

**Assigning Variables:**

You use the equals sign (`=`) to assign a value to a variable name.

```python
# Assign the text "Jane Austen" to the variable named 'author'
author = "Jane Austen"

# Assign the number 1813 to the variable named 'publication_year'
publication_year = 1813

# Now we can print the variables to see their values
print(author)
print(publication_year)
```

Run the code block above. You'll see the _values_ stored in the variables printed.

**Basic Data Types:**

Variables can hold different _types_ of data. Here are a few fundamental ones:

* **String (str):** Textual data, enclosed in single (') or double (") quotes.
	
	* Example: "Pride and Prejudice", 'Mr. Darcy'
* **Integer (int):** Whole numbers (no decimals).
	
	* Example: 1813, \-5, 100
* **Float (float):** Numbers with decimals.
	
	* Example: 3.14, \-0.5, 99.0

Python usually figures out the type automatically when you assign a value.

**Using Variables:**

Variables make code easier to read and reuse.

```python
book_title = "Pride and Prejudice"
author = "Jane Austen"
publication_year = 1813

# We can combine strings and variables using f-strings (formatted string literals)
# Put an 'f' before the opening quote and variables in curly braces {}
print(f"The book '{book_title}' was written by {author}.")
print(f"It was published in {publication_year}.")

# You can reassign variables
publication_year = 1815 # Maybe it was a later edition we care about
print(f"We are focusing on the edition from {publication_year}.")
```

Experiment by changing the values assigned to the variables and re-running the code block.

➡️ **Next Step:** [[04: Lists - Storing Multiple Items|DH Beginner - 04 Lists - Storing Multiple Items]]

---

_Back to [[Welcome to the DHP]] | [[DH Beginner - Start Here]]_
 