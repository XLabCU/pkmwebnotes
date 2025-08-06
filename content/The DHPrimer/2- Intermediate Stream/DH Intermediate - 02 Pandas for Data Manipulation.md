---
title: "02: Pandas for Data Manipulation"
created: 2025-08-05T12:57:24.739Z
tags: [project]
---
# DH Intermediate - 02: Pandas for Data Manipulation

Now that we know how to load data into a Pandas DataFrame using `pd.read_csv()`, let's explore how to select, filter, and perform basic calculations on that data. We'll continue using the `austen_books.csv` file created in the previous note.

**Loading the Data Again:**

```python
import pandas as pd

csv_filename = "austen_books.csv"

try:
    books_df = pd.read_csv(csv_filename)
    print("--- Original DataFrame ---")
    print(books_df)
except FileNotFoundError:
    print(f"Error: File '{csv_filename}' not found.")
    print("Please run the code in '[[DH Intermediate - 01 Working with Tabular Data - Intro to Pandas]]' first.")
except Exception as e:
    print(f"An error occurred: {e}")

```

**Selecting Columns:**

You can select one or more columns easily.

```python
if 'books_df' in locals(): # Check if books_df exists
    # Select a single column (returns a Series)
    titles = books_df['Title']
    print("\n--- Selecting 'Title' column (Series) ---")
    print(titles)
    print(f"Type: {type(titles)}")

    # Select multiple columns (returns a DataFrame)
    # Pass a list of column names
    title_year = books_df[['Title', 'Year']]
    print("\n--- Selecting 'Title' and 'Year' columns (DataFrame) ---")
    print(title_year)
    print(f"Type: {type(title_year)}")
```

**Selecting Rows (Filtering):**

Filtering rows based on conditions is a fundamental operation. We use "boolean indexing".

```python
if 'books_df' in locals():
    # Condition: Select books published after 1814
    condition = books_df['Year'] > 1814
    print("\n--- Boolean Series for Year > 1814 ---")
    print(condition) # Shows True/False for each row

    # Apply the condition to the DataFrame
    late_books = books_df[condition]
    print("\n--- Books published after 1814 ---")
    print(late_books)

    # Combine conditions: Books published after 1814 AND are 'Novel of manners'
    # Use & for AND, | for OR. Parentheses are important!
    combined_condition = (books_df['Year'] > 1814) & (books_df['Genre'] == 'Novel of manners')
    late_manners_books = books_df[combined_condition]
    print("\n--- 'Novel of manners' published after 1814 ---")
    print(late_manners_books)

    # Select using .loc for label-based indexing (can select rows and columns)
    # Get rows with index 1 and 3, and only 'Title' and 'Author' columns
    specific_selection = books_df.loc[[1, 3], ['Title', 'Author']]
    print("\n--- Selecting specific rows/columns with .loc ---")
    print(specific_selection)
```

**Basic Calculations:**

Pandas makes performing calculations on columns straightforward.

```python
if 'books_df' in locals():
    # Get basic descriptive statistics for numerical columns
    print("\n--- Descriptive Statistics (.describe()) ---")
    print(books_df.describe()) # Only works on numerical columns by default

    # Calculate the average publication year
    avg_year = books_df['Year'].mean()
    print(f"\nAverage Publication Year: {avg_year:.2f}") # Format to 2 decimal places

    # Count occurrences of each genre
    genre_counts = books_df['Genre'].value_counts()
    print("\n--- Genre Counts (.value_counts()) ---")
    print(genre_counts)
```

**Handling Missing Data (Briefly):**

Real-world data often has missing values (represented as `NaN` - Not a Number in Pandas).

```python
# Let's intentionally add a missing value
if 'books_df' in locals():
    import numpy as np # numpy is often used with pandas, provides np.nan
    books_df_missing = books_df.copy() # Work on a copy
    books_df_missing.loc[2, 'Year'] = np.nan # Set year for Mansfield Park to NaN
    print("\n--- DataFrame with Missing Value ---")
    print(books_df_missing)

    print("\n--- Check for Missing Values (.isna()) ---")
    print(books_df_missing.isna()) # Shows True where data is missing

    print("\n--- Sum of Missing Values per Column ---")
    print(books_df_missing.isna().sum())

    # Options: Drop rows/columns with NaN or fill them
    # print("\n--- Dropping rows with NaN (.dropna()) ---")
    # print(books_df_missing.dropna())
    # print("\n--- Filling NaN with a value (.fillna()) ---")
    # print(books_df_missing.fillna({'Year': books_df_missing['Year'].median()})) # Fill with median year
```

**Key Concepts:**

*   **Column Selection:** `df['Column']` or `df[['Col1', 'Col2']]`
*   **Boolean Indexing:** `df[df['Column'] > value]`
*   **`.loc`:** Selection by labels (row index labels, column names).
*   **`.iloc`:** Selection by integer position (like list slicing). *(Not shown above, but useful)*
*   **Vectorized Operations:** Calculations like `.mean()`, `.sum()` operate on entire columns efficiently.
*   **`.value_counts()`:** Quickly tabulate unique values in a Series.
*   **`.isna()`, `.dropna()`, `.fillna()`:** Basic tools for handling missing data.

Pandas offers a vast range of functionalities. These basic selection and manipulation techniques are essential for almost any data analysis task in DH.

➡️ **Next Step:** [[DH Intermediate - 03 Introduction to Data Visualization - Matplotlib & Seaborn]]

---

*Back to [[Welcome to the DHP]] | [[DH Intermediate - Start Here]]*