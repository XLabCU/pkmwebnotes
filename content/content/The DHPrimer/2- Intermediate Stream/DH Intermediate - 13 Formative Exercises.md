---
title: "Formative Assessment: Exploring Chronicling America"
created: 2025-08-05T12:57:24.739Z
tags: [project]
---
## Formative Assessment: Exploring Chronicling America

**Objective:** To apply intermediate Python skills (API interaction, JSON handling, Pandas, pagination) and Digital Humanities concepts to formulate a research question answerable via the Chronicling America newspaper archive, devise an analytical plan, execute parts of that plan using code, and integrate findings with secondary readings.

**Environment:** This Tangent Workspace

**Overall Instructions for Students:**

1.  **Create a Project Folder:** In Tangent, create a new **Folder** (not a Workspace) named `ChronAm_DH_Project`. All notes related to this assessment should be saved within this folder.
2.  **Familiarize Yourself with the Data Source:** Explore the Chronicling America website ([https://chroniclingamerica.loc.gov/](https://chroniclingamerica.loc.gov/)) and its API documentation ([https://chroniclingamerica.loc.gov/about/api/](https://chroniclingamerica.loc.gov/about/api/)) to understand the scope of the data and the available search parameters.
3.  **Complete the Parts Below:** Work through the following parts by creating and editing the specified markdown notes within your project folder. Pay close attention to instructions regarding wikilinking, API etiquette (rate limiting), and code execution.

**Tip** if you're not sure _where_ Tangent is saving your materials (where is your workspace?) you can run this python snippet to find out:

```python
import os
print(f"Current working directory: \n {os.getcwd()}")
```

In what follows, you will be directed to create various notes, and templates for what should go into these notes. _You can create more notes as you need_. Ideally, a good rule-of-thumb is that one note contains one main idea/process, and then you interlink as you need.

---

### Part 1: Planning Your Project

**Create Note:** [[chronam-planning-moc]]

A 'moc' is a 'map-of-content'; you can think of this as being similar to a table of contents. We make an overview moc that will serve as the point-of-entry into all of the interconnected notes for this exercise (whereas a table of contents would list _all_ of your materials, organized hierarchically.) If you ctrl/cmd+click the wikilink above, Tangent will create the new note for you.

**Content for `chronam-planning-moc`:**

Copy this block (without the back-ticks and the word 'markdown') into that new note. Replace the text with your own thoughts as directed.

```markdown
# Chronicling America DH Project Plan (MOC)

## 1. Research Question

*Formulate a specific research question about historical American newspapers (1777-1963) that you can explore using the Chronicling America API and the DH techniques learned so far (API requests, JSON parsing, data structuring/analysis with Pandas, potentially basic text analysis on OCR snippets if feasible). Focus on topics, terms, or coverage patterns within specific date ranges, states, or newspaper titles.*

**My Question:** [***Students write their research question here.*** *Example: How did the frequency of articles mentioning "transcontinental railroad" change in California newspapers between 1860 and 1880?* or *Example: Which states had the most newspaper pages mentioning the term "influenza" during the year 1918?*]

## 2. Data Source

*   **Primary Data:** Chronicling America Search API ([https://chroniclingamerica.loc.gov/about/api/](https://chroniclingamerica.loc.gov/about/api/))

## 3. Methodology Outline

*Outline the step-by-step process you will follow to address your research question using the API. For each step involving a technique covered in the tutorials, **add a wikilink** to the relevant tutorial note.*

**Steps:**

1.  [***Example Step:*** Define search parameters (keywords, date range, state, etc.) based on the research question and API documentation.]
2.  [***Example Step:*** Construct and send the initial API request using `requests` [[DH Intermediate - 10 Working with an API - Chronicling America]].]
3.  [***Example Step:*** Parse the JSON response [[DH Intermediate - 09 Introduction to APIs and JSON]].]
4.  [***Example Step:*** Check `totalItems` and implement pagination logic if necessary, making sure to include pauses (`time.sleep()`) between requests to respect rate limits [[DH Intermediate - 11 Handling API Pagination and Rate Limiting]].]
5.  [***Example Step:*** Extract relevant metadata (e.g., date, newspaper title, state, page URL) for each matching item.]
6.  [***Example Step:*** Store the extracted metadata, potentially in a Pandas DataFrame [[DH Intermediate - 01 Working with Tabular Data - Intro to Pandas]] & [[DH Intermediate - 02 Pandas for Data Manipulation]].]
7.  [***Example Step:*** Analyze the collected metadata (e.g., count results per year/state, identify key newspapers). ]
8.  [***Optional Example Step:*** If feasible and necessary for the question, fetch OCR text for a *small sample* of relevant pages to examine context [[DH Intermediate - 12 Fetching and Analyzing Content from API Results (OCR Text)]]. *Remember strict rate limiting!*]
9.  [***Example Step:*** Write up findings in `[[chronam-analysis.md]]`, incorporating insights from `[[Part 3: Readings Integration]]`.]
10. ... [*Students add/modify steps based on their question*] ...

## 4. Potential Challenges

*Identify any anticipated difficulties.*

*   [***Example:*** Defining effective API search terms to capture relevant articles without too much noise.]
*   [***Example:*** Handling potential API errors or timeouts.]
*   [***Example:*** Managing the potentially large number of results and the need for careful pagination/rate limiting.]
*   [***Example:*** Inconsistent OCR quality if analyzing text content.]
*   [***Example:*** Interpreting search hit counts vs. actual article relevance.]
*   [***Students list potential challenges here.***]

## 5. Expected Outcome

*Describe what you hope to produce or learn.*

*   [***Example:*** A dataset (CSV) and summary statistics showing the trend of "transcontinental railroad" mentions in CA newspapers over the specified period. Possibly a simple line graph visualizing the trend.]
*   [***Students describe their expected outcome here.***]

```

---

### Part 2: Coding Exercises

**Instructions:** Create the following notes. For each note, first read the objective and the partial code. In the "Prediction" section, write down what you **think** the code *should* do or output *before* you modify or run it. Then, complete the code as instructed, run the code block, and write your observations in the "Reflection" section, comparing the actual output to your prediction.

**Create Note:** [[coding-exercise-01-api-request]]

Remember to add this to your MOC.

**Content:**

```markdown
# Coding Exercise 01: Basic API Request

**Objective:** Construct parameters for a Chronicling America search and make a single API GET request, then parse the JSON response.

**Prediction:**
[*Students write here: What data structure (type) do you expect `data` to be after `response.json()`? What kind of information might be top-level keys in that structure based on the API docs or previous examples?*]

**Partial Code:**

NB that you need _three_ backticks to set off the code correctly so that Tangent will recognize it.

``python
import requests
import json

# Define the API endpoint
base_url = "https://chroniclingamerica.loc.gov/search/pages/results/"

# Define search parameters (simple example: find newspaper pages mentioning 'library' in Washington DC in 1910)
params = {
    'state':  # COMPLETE THIS LINE
    'andtext': # COMPLETE THIS LINE
    'dateFilterType': 'yearRange',
    'date1': # COMPLETE THIS LINE
    'date2': # COMPLETE THIS LINE
    'rows': 5, # Limit results for this exercise
    'format': # COMPLETE THIS LINE
}

print("Making API request...")
try:
    # Make the GET request using the 'requests' library, passing the base_url and params
    response = requests. # COMPLETE THIS LINE: use the correct requests function

    # Raise an exception if the request was unsuccessful (status code 4xx or 5xx)
    response.raise_for_status()
    print(f"Request successful (Status Code: {response.status_code})")

    # Parse the JSON response into a Python dictionary
    data = response. # COMPLETE THIS LINE: use the method to parse JSON

    # Print the type of the parsed data and its top-level keys
    print(f"\nType of parsed data: {type(data)}")
    if isinstance(data, dict):
        print(f"Top-level keys in response: {list(data.keys())}")

    # Optional: pretty print a small part of the JSON data
    # print("\n--- Sample JSON Response ---")
    # print(json.dumps(data, indent=2, sort_keys=True)[:1000] + "\n...") # Print first 1000 chars

except requests.exceptions.RequestException as e:
    print(f"Request failed: {e}")
except json.JSONDecodeError:
    print("Failed to decode JSON response.")
    print(f"Response text: {response.text[:500]}...") # Show beginning of text if not JSON
except Exception as e:
    print(f"An error occurred: {e}")
``

**Completion & Execution:**

*Complete the code lines above, then run the code block. What happened? What errors or error messages did you encounter? Use Stackoverflow to try to find solutions; do not use ChatGPT or similar to fix the code (for what would be the point of that?)*]

**Reflection:**
[*Students write here: Did the type and keys of the `data` object match your prediction? If you uncommented the print statement, what did the structure of the response look like?*]
```

---

**Create Note:** [[coding-exercise-02-json-navigation]]

Remember to add these wikilinks to your MOC.

**Content:**

```markdown
# Coding Exercise 02: Navigating JSON Data

**Objective:** Given a sample JSON structure (as a Python dictionary), extract specific nested information.

**Prediction:**
[*Students write here: Based on the `sample_data` structure, what specific values do you expect the `print` statements at the end to output for `total`, `first_item_title`, and `first_item_lccn`?*]

**Partial Code:**

NB that you need _three_ backticks to set off the code correctly so that Tangent will recognize it.

``python
# Sample Python dictionary mimicking a ChronAm API JSON response
sample_data = {
  "totalItems": 123,
  "endIndex": 20,
  "startIndex": 1,
  "itemsPerPage": 20,
  "items": [
    {
      "sequence": 5,
      "lccn": "sn83045462",
      "edition": 1,
      "frequency": "Daily",
      "url": "https://chroniclingamerica.loc.gov/lccn/sn83045462/1918-09-01/ed-1/seq-5.json",
      "date": "19180901",
      "title_normal": "The Washington times.",
      "page": "5",
      "county": ["District of Columbia"],
      "state": ["District of Columbia"],
      "city": ["Washington"],
      "id": "/lccn/sn83045462/1918-09-01/ed-1/seq-5/",
      "subject": ["Newspapers."],
      "ocr_eng": "..." # OCR text omitted for brevity
    },
    {
      "sequence": 1,
      "lccn": "sn84026749",
      "edition": 1,
      "frequency": "Daily",
      "url": "https://chroniclingamerica.loc.gov/lccn/sn84026749/1918-11-11/ed-1/seq-1.json",
      "date": "19181111",
      "title_normal": "The Evening star.",
      "page": "1",
      "county": ["District of Columbia"],
      "state": ["District of Columbia"],
      "city": ["Washington"],
      "id": "/lccn/sn84026749/1918-11-11/ed-1/seq-1/",
      "subject": ["Newspapers."],
      "ocr_eng": "..."
    }
  ]
}

# Extract the total number of items
total = sample_data # COMPLETE THIS LINE: access the 'totalItems' key

# Extract the list of result items
results_list = sample_data # COMPLETE THIS LINE: access the 'items' key

# Check if results_list is not empty before accessing elements
first_item_title = "N/A"
first_item_lccn = "N/A"
if results_list: # Check if the list has items
    # Access the first item in the list (index 0)
    first_item = results_list[# COMPLETE THIS LINE: use the correct index]

    # Extract the 'title_normal' and 'lccn' from the first item
    first_item_title = first_item[# COMPLETE THIS LINE: access the title key]
    first_item_lccn = first_item[# COMPLETE THIS LINE: access the lccn key]

print(f"Total Items: {total}")
print(f"Title of First Result: {first_item_title}")
print(f"LCCN of First Result: {first_item_lccn}")

``

**Completion & Execution:**
[*Complete the code lines above, then run the code block.*]

**Reflection:**
[*Students write here: Did the extracted values match your prediction? What potential errors could occur if you tried to access `results_list[0]` without checking if the list was empty first?*]

---
```

**Create Note:** [[coding-exercise-03-rate-limit]]

Remember to add this note to your MOC.

**Content:**

```markdown
# Coding Exercise 03: Implementing a Pause

**Objective:** Use the `time` module to pause execution within a loop, simulating polite API usage.

**Prediction:**
[*Students write here: Describe the expected output of this code. How much time will elapse between the printing of "Processing item 1" and "Processing item 2"?*]

**Partial Code:**

NB that you need _three_ backticks to set off the code correctly so that Tangent will recognize it.


``python
import time # Import the time module

items_to_process = [ "ItemA", "ItemB", "ItemC" ]
pause_duration = 1.5 # Seconds to pause

print("Starting processing...")

for i, item in enumerate(items_to_process):
    print(f"Processing item {i+1}: {item}")

    # --- Simulate doing work for this item ---
    # (In a real scenario, this might be an API call or complex calculation)
    # --- End simulated work ---

    # Check if it's NOT the last item before pausing
    if i < len(items_to_process) - 1:
         print(f"  Pausing for {pause_duration} seconds before next item...")
         # COMPLETE THIS LINE: call the function from the 'time' module to pause execution
         # for the specified 'pause_duration'

print("\nProcessing finished.")
``

**Completion & Execution:**
[*Complete the code line above, then run the code block.*]

**Reflection:**
[*Students write here: Did the code pause as expected? Why is it generally good practice to check if it's the last item before pausing in a loop like this when interacting with external resources?*]

```

---

### Part 3: Readings Integration

**Instructions:**

1.  Find 3 or 4 relevant scholarly articles or book chapters. Try searching [Humanities Commons](https://hcommons.org/search/) or [OpenAlex](https://openalex.org/) These could be about:
    *   The Chronicling America project or digital newspaper archives in general.
    *   The specific historical topic, period, or location you are investigating.
    *   Methodological considerations for using digitized newspapers in historical research.
2.  For each reading, create a new note in your project folder (e.g., `reading-AuthorLastName-Year`).
3.  In each reading note, include: Full Citation, Summary (2-3 sentences), Relevance (1-2 sentences explaining connection to your project), (Optional) Key Quotes. For quickly generating formatted bibliography, [https://zbib.org/](https://zbib.org/) is handy. (Nb - while not implemented here, software similar to Tangent like [Obsidian](https://obsidian.md) have many plugins that allow one to plumb Zotero directly into the note making workflow, and you may want to investigate those possibilities. A Tangent Workspace, since it saves all your work locally, _can also_ be opened by Obsidian!).

For reading notes, this structure might be useful:

```markdown
**NB** THIS IS A COMPLETELY FABRICATED REFERENCE. THE ACTUAL JDLS ONLY HAS ONE ISSUE [which is available here.](https://journals.psu.edu/dls/issue/archive)

# Reading: Smith (2020)

**Citation:** Smith, J. (2020). Monstrous Frequencies: A Computational Analysis of Keywords in Shelley's *Frankenstein*. *Journal of Digital Literary Studies, 5*(2), 45-67.

**Summary:** Smith performs a frequency analysis of words related to nature and the unnatural across the 1818 edition. They argue that the quantitative prevalence of unnatural terminology directly correlates with the novel's tragic events.

**Relevance:** This directly informs my project on creation/destruction keywords by providing a methodological precedent and suggesting that frequency changes are meaningful indicators of thematic shifts in the novel.

**Key Quotes:**
> "The stark increase in words like 'wretch', 'daemon', and 'unnatural' in Volume III quantitatively underscores the narrative's descent..." (p. 58)

** Observations**

_This is something I use in my own notetaking that you might find helpful_

Using a table-like structure, on the left you can indicate pg numbers and a brief paraphrase of the element of the article that has caught your eye. On the right, you can write your own memo-to-self about why it's important, or what other element it speaks to, questions it raises, and so on.

| Source.       | Observation.  |
| ------------- | ------------- |
| p58 "The stark increase in words like 'wretch', 'daemon', and 'unnatural' in Volume III quantitatively underscores the narrative's descent..." | How were these words used in Volume I? |
| p64 a summary of Victor's changing mental state | Call back to pg 58 & 32 |

```
---

### Part 4: Analysis and Write-up

**Instructions:**

1.  Execute the analysis plan outlined in your [[chronam-planning-moc.md]], making sure to handle pagination and rate limiting appropriately. Store your collected data (e.g., in a Pandas DataFrame saved to a CSV file within your project folder).
2.  Create a final note named [[chronam-analysis]].
3.  In this note, present your findings.

**Create Note:** [[chronam-analysis]]

**Content for chronam-analysis:**

```markdown
# Analysis of [Your Research Question Topic] using the Chronicling America API and data

## Research Question

*Restate your research question from [[chronam-planning-moc]].*

[***Student writes question here.***]

## Method

*Briefly describe the steps you took, referencing your plan, API details, and relevant tutorials. Mention parameters used, how pagination was handled, and any data storage methods.*

Following the plan in [[chronam-planning-moc.md]], the Chronicling America API [[DH Intermediate - 10 Working with an API - Chronicling America]] was queried using the parameters [***Student lists key parameters***]. The JSON response [[DH Intermediate - 09 Introduction to APIs and JSON]] was processed, and pagination was handled by iterating through pages with a pause of X seconds between requests [[DH Intermediate - 11 Handling API Pagination and Rate Limiting]]. Relevant metadata was extracted and stored [***Example: in a Pandas DataFrame saved as `[[results.csv]]`***].

*(Include snippets of code output, tables summarizing results, or simple visualizations if relevant and generated)*

NB remember that code is set off using three backticks.

``python
# Example: Show the head of the resulting Pandas DataFrame (if applicable)
# import pandas as pd
# try:
#    results_df = pd.read_csv("results.csv")
#    print(results_df.head())
# except FileNotFoundError:
#    print("Results CSV not found.")
``

## Findings

*Present the main results of your analysis. This could be trends over time, comparisons between states/newspapers, lists of key findings, etc.*

[***Students describe their findings here. Example:*** The query returned Y total matching pages. Analysis of the metadata showed a peak in mentions of "influenza" in Oregon newspapers during October and November of 1918. The 'Oregon Daily Journal' had the highest number of hits for this term during that year.]

## Discussion

*Interpret your findings in the context of your research question. What do the patterns suggest? **Connect your findings to the insights from your secondary readings using wikilinks.** *

e.g.:

The concentration of "influenza" mentions in late 1918 directly reflects the historical timeline of the Spanish Flu pandemic. This quantitative finding provides newspaper-level evidence corroborating broader historical accounts like [[reading-Author1-Year]]. The prominence of the 'Oregon Daily Journal' might relate to points raised in [[reading-Author2-Year]] about the role of major urban newspapers in disseminating public health information during crises. [***Students adapt and expand, linking to their specific readings.***]

## Limitations and Future Work

*Acknowledge any limitations of your analysis (e.g., search term limitations, API constraints, reliance on metadata vs. full text, OCR issues) and suggest potential next steps.*

e.g:

This analysis relied solely on the keyword "influenza" and did not capture related terms like "flu" or "grippe". The results represent page-level hits, not necessarily the prominence or context within the article. Future work could involve [[DH Intermediate - 12 Fetching and Analyzing Content from API Results (OCR Text)]] for a sample of pages to analyze the context of the mentions, or using more sophisticated search strategies. Comparing results with archives from other states could also provide a broader perspective.

```