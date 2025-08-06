---
title: "01: Working with Tabular Data - Intro to Pandas"
created: 2025-08-05T12:57:24.739Z
tags: [intermediate]
---
# DH Intermediate - 01: Working with Tabular Data - Intro to Pandas

Much humanities data comes in, or can be structured into, tables: metadata about books, survey results, counts of features over time, etc. Spreadsheets are common, often saved as CSV (Comma Separated Values) files.

The **Pandas** library is the cornerstone of data manipulation in Python. It provides powerful, flexible, and efficient data structures designed to make working with "relational" or "labeled" data both easy and intuitive.

**Installation:**

If you don't have Pandas installed, you'll need to install it. In many Python environments (like Anaconda), it comes pre-installed. If not, you can typically install it using pip (Python's package installer) in your terminal or command prompt:
`pip install pandas`
*(You usually don't run pip commands inside the notebook itself, but in your system's terminal.)*

**Core Pandas Data Structures:**

1.  **`Series`**: A one-dimensional labeled array capable of holding any data type (integers, strings, floats, Python objects, etc.). Think of it like a single column in a spreadsheet with an index.
2.  **`DataFrame`**: A two-dimensional labeled data structure with columns of potentially different types. Think of it like a full spreadsheet or an SQL table. It's the most commonly used Pandas object.

**Let's Create Some Data:**

```python
import pandas as pd # Standard convention to import pandas as 'pd'

# Creating a Series
years = pd.Series([1811, 1813, 1814, 1815], name='Publication Year')
print("--- Pandas Series ---")
print(years)
print("\n")

# Creating a DataFrame from a dictionary
# Keys become column headers, values are lists of column data
data = {
    'Title': ["Sense and Sensibility", "Pride and Prejudice", "Mansfield Park", "Emma"],
    'Author': ["Jane Austen"] * 4, # Repeat author name 4 times
    'Year': [1811, 1813, 1814, 1815]
}
austen_df = pd.DataFrame(data)

print("--- Pandas DataFrame ---")
print(austen_df)
```

**Reading Data from a File (CSV):**

Creating data manually is fine for small examples, but usually, you'll load data from a file. Let's create a sample CSV first (similar to how we created a text file before).

```python
# Create some CSV content as a string
csv_content = """Title,Author,Year,Genre
Sense and Sensibility,Jane Austen,1811,Novel of manners
Pride and Prejudice,Jane Austen,1813,Novel of manners
Mansfield Park,Jane Austen,1814,Novel of manners
Emma,Jane Austen,1815,Novel of manners
Northanger Abbey,Jane Austen,1817,Gothic parody
Persuasion,Jane Austen,1817,Novel of manners
"""

csv_filename = "austen_books.csv"

# Write the string to a CSV file
try:
    with open(csv_filename, 'w', encoding='utf-8') as f:
        f.write(csv_content)
    print(f"File '{csv_filename}' created successfully.")

    # Now, read the CSV file using Pandas
    print(f"\n--- Reading '{csv_filename}' with Pandas ---")
    books_df = pd.read_csv(csv_filename)

    # Display the first few rows using .head()
    print("\n--- DataFrame Head ---")
    print(books_df.head()) # Shows first 5 rows by default

    # Get basic info about the DataFrame
    print("\n--- DataFrame Info ---")
    books_df.info()

except Exception as e:
    print(f"An error occurred: {e}")

```

**Explanation:**

*   `import pandas as pd`: Imports the library and gives it a shorter alias `pd`.
*   `pd.Series()`: Creates a Series object.
*   `pd.DataFrame()`: Creates a DataFrame object. We used a dictionary where keys are column names and values are lists of data.
*   `pd.read_csv('filename.csv')`: This is the key function for loading data from CSV files into a DataFrame. Pandas automatically detects the header row and data types (usually).
*   `.head(n)`: A DataFrame method to view the first `n` rows (default is 5). Very useful for quickly inspecting large tables.
*   `.info()`: A DataFrame method that provides a concise summary: index type, column names, non-null counts, and data types per column, plus memory usage.

Pandas makes loading and initially inspecting structured data incredibly simple compared to manual file reading and parsing.

➡️ **Next Step:** [[DH Intermediate - 02 Pandas for Data Manipulation]]

---

*Back to [[Welcome to the DHP]] | [[DH Intermediate - Start Here]]*