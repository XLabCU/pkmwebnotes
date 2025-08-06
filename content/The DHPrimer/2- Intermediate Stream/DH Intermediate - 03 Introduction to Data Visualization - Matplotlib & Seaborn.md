---
title: "03: Introduction to Data Visualization - Matplotlib & Seaborn"
created: 2025-08-05T12:57:24.739Z
tags: [project]
---
# DH Intermediate - 03: Introduction to Data Visualization - Matplotlib & Seaborn

Analyzing data is one thing, but effectively communicating your findings often requires visualization. Charts and graphs can reveal patterns, trends, and outliers more clearly than tables of numbers.

Two core Python libraries for visualization are:

1.  **Matplotlib**: The foundational plotting library. Highly customizable but can sometimes be verbose for standard plots. Its `pyplot` module provides a MATLAB-like interface.
2.  **Seaborn**: Built on top of Matplotlib, Seaborn provides a high-level interface for drawing attractive and informative statistical graphics. It often requires less code for complex, common plot types and integrates seamlessly with Pandas DataFrames.

**Installation:**

If not already installed:
`pip install matplotlib seaborn`

**Basic Plotting with Matplotlib:**

Let's start with a very simple plot using Matplotlib directly.

```python
import matplotlib.pyplot as plt # Standard convention

# Sample data
x_values = [1, 2, 3, 4, 5]
y_values = [2, 3, 5, 7, 11] # Some prime numbers

# Create a simple line plot
plt.plot(x_values, y_values)

# Add labels and title
plt.xlabel("Index")
plt.ylabel("Prime Number")
plt.title("Simple Line Plot")

# Display the plot
plt.show() # This command renders the plot
```

**Visualizing Pandas Data with Seaborn:**

Seaborn makes plotting directly from DataFrames very easy. Let's use our `austen_books.csv` data again.

```python
import pandas as pd
import seaborn as sns # Standard convention
import matplotlib.pyplot as plt # Still often needed for customization

csv_filename = "austen_books.csv"

try:
    books_df = pd.read_csv(csv_filename)
    print("--- Data Loaded ---")
    print(books_df.head())

    # --- Example 1: Bar Plot of Genre Counts ---
    plt.figure(figsize=(8, 5)) # Optional: Adjust figure size
    sns.countplot(data=books_df, y='Genre') # Use countplot for frequencies
    plt.title('Count of Books by Genre')
    plt.xlabel('Number of Books')
    plt.ylabel('Genre')
    plt.tight_layout() # Adjust layout to prevent labels overlapping
    plt.show()

    # --- Example 2: Histogram of Publication Years ---
    plt.figure(figsize=(8, 5))
    sns.histplot(data=books_df, x='Year', bins=5) # bins controls number of bars
    plt.title('Distribution of Publication Years')
    plt.xlabel('Publication Year')
    plt.ylabel('Frequency (Count)')
    plt.show()

    # --- Example 3: Relationship Plot (if we had more numeric data) ---
    # Let's add a fictional 'Pages' column for demonstration
    books_df['Pages'] = [432, 279, 461, 470, 280, 291] # Example page counts
    plt.figure(figsize=(8, 5))
    sns.scatterplot(data=books_df, x='Year', y='Pages', hue='Genre') # Color points by Genre
    plt.title('Book Pages vs. Publication Year')
    plt.xlabel('Publication Year')
    plt.ylabel('Number of Pages')
    plt.legend(title='Genre', bbox_to_anchor=(1.05, 1), loc='upper left') # Move legend outside
    plt.tight_layout()
    plt.show()


except FileNotFoundError:
    print(f"Error: File '{csv_filename}' not found.")
    print("Please run the code in '[[DH Intermediate - 01 Working with Tabular Data - Intro to Pandas]]' first.")
except Exception as e:
    print(f"An error occurred: {e}")

```

**Explanation:**

*   `import matplotlib.pyplot as plt` and `import seaborn as sns`: Standard imports.
*   `plt.figure(figsize=(width, height))`: Creates a figure canvas; adjusting the size can make plots more readable.
*   `sns.countplot(data=df, x='col')` or `sns.countplot(data=df, y='col')`: Creates a bar chart showing counts of unique values in a categorical column. `x` makes vertical bars, `y` makes horizontal bars.
*   `sns.histplot(data=df, x='col', bins=n)`: Creates a histogram showing the distribution of a numerical column. `bins` suggests how many intervals to divide the data into.
*   `sns.scatterplot(data=df, x='col1', y='col2', hue='col3')`: Creates a scatter plot to visualize the relationship between two numerical columns (`col1`, `col2`). `hue` assigns different colors to points based on a third (often categorical) column (`col3`).
*   `plt.title()`, `plt.xlabel()`, `plt.ylabel()`: Used to add descriptive labels to the plot (even when using Seaborn).
*   `plt.legend()`: Customizes the legend, useful when using `hue`.
*   `plt.tight_layout()`: Automatically adjusts plot parameters for a tight layout.
*   `plt.show()`: Displays the generated plot(s).

Seaborn offers many other plot types (box plots, violin plots, heatmaps, pair plots, etc.). Exploring the [Seaborn gallery](https://seaborn.pydata.org/examples/index.html) is a great way to see the possibilities. Effective visualization is key to exploring and communicating insights from DH data.

➡️ **Next Step:** [[DH Intermediate - 04 Acquiring Web Data - Introduction to Web Scraping]]

---

*Back to [[Welcome to the DHP]] | [[DH Intermediate - Start Here]]*