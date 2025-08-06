---
title: "04: Lists - Storing Multiple Items"
created: 2025-08-05T12:57:24.739Z
tags: [beginner]
---

# DH Beginner - 04: Lists - Storing Multiple Items

Often, you need to store and work with a collection of items, like a list of book titles or chapter names. In Python, the most common way to do this is using a **list**.

**Creating a List:**

You create a list by enclosing comma-separated items within square brackets `[]`.

```python
# A list of strings (book titles)
austen_novels = ["Sense and Sensibility", "Pride and Prejudice", "Mansfield Park", "Emma"]

# A list of integers (publication years)
# Note: Order corresponds to the titles above
publication_years = [1811, 1813, 1814, 1815]

# You can print the whole list
print(austen_novels)
print(publication_years)

# A list can contain different data types, though it's often clearer
# to keep them consistent if they represent similar things.
mixed_list = ["Northanger Abbey", 1817, "Persuasion", 1817]
print(mixed_list)
```

**Accessing List Items:**

You can access individual items in a list using their **index**. Python uses **zero-based indexing**, meaning the first item is at index 0, the second at index 1, and so on.

```python
austen_novels = ["Sense and Sensibility", "Pride and Prejudice", "Mansfield Park", "Emma"]

# Get the first item (index 0)
first_novel = austen_novels[0]
print(f"The first novel is: {first_novel}")

# Get the third item (index 2)
third_novel = austen_novels[2]
print(f"The third novel is: {third_novel}")

# You can use negative indices to count from the end
# -1 is the last item, -2 is the second-to-last, etc.
last_novel = austen_novels[-1]
print(f"The last novel in the list is: {last_novel}")
```

**Modifying Lists:**

Lists are **mutable**, meaning you can change them after they are created.

```python
# Let's start with our list again
austen_novels = ["Sense and Sensibility", "Pride and Prejudice", "Mansfield Park", "Emma"]
print(f"Original list: {austen_novels}")

# Add an item to the end using .append()
austen_novels.append("Northanger Abbey")
print(f"List after append: {austen_novels}")

# Change an item using its index
# Let's say we made a mistake and want to fix the second item
austen_novels[1] = "Pride & Prejudice" # Corrected title
print(f"List after correction: {austen_novels}")

# Get the number of items in a list using len()
num_novels = len(austen_novels)
print(f"There are {num_novels} novels in the list.")
```


Lists are fundamental for working with sequences of data, which is very common in DH (e.g., lines in a text, words in a sentence, rows in a table).

➡️ **Next Step:** [[05: Creating a Sample Text File|DH Beginner - 05 Creating a Sample Text File]] _(We need some text data to work with!)_

---

_Back to [[Welcome to the DHP]] | [[DH Beginner - Start Here]]_

