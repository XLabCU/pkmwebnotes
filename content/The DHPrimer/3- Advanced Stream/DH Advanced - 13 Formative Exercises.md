---
title: "13: Formative Assessment: Exploring the Met Collection"
created: 2025-08-05T12:57:24.739Z
tags: [advanced]
---
## Formative Assessment: Exploring the Met Collection

**Objective:** To apply advanced Python skills (complex API interaction, data structuring, choice of advanced analytical techniques) and Digital Humanities concepts to formulate a research question about The Met's collection, devise an analytical plan, execute parts of that plan using code, and integrate findings with secondary readings.

**Environment:** This Tangent Workspace

**Overall Instructions for Students:**

1.  **Create a Project Folder:** In Tangent, create a new **Folder** (not a Workspace) named `Met_DH_Project`. All notes related to this assessment should be saved within this folder.
2.  **Familiarize Yourself with the Data Source:** Thoroughly review The Metropolitan Museum of Art Collection API documentation ([https://metmuseum.github.io/](https://metmuseum.github.io/)). Understand the available endpoints (especially `/objects` and `/objects/{objectID}`), parameters, and the structure of the data returned. Note the Open Access nature and usage guidelines.
3.  **Choose a Focus:** The Met collection is vast. For this assessment, **choose a specific subset** to focus on (e.g., objects from a particular department, culture, medium, or time period that has a reasonable number of objects - perhaps a few hundred to a few thousand initially). This focus will guide your research question and analysis.
4.  **Complete the Parts Below:** Work through the following parts by creating and editing the specified markdown notes within your project folder. Pay close attention to instructions regarding wikilinking, API usage, and code execution.

**Tip** if you're not sure _where_ Tangent is saving your materials (where is your workspace?) you can run this python snippet to find out:

```python
import os
print(f"Current working directory: \n {os.getcwd()}")
```

In what follows, you will be directed to create various notes, and templates for what should go into these notes. _You can create more notes as you need_. Ideally, a good rule-of-thumb is that one note contains one main idea/process, and then you interlink as you need.

---

### Part 1: Planning Your Project

**Create Note:** [[met-planning-moc]]

A 'moc' is a 'map-of-content'; you can think of this as being similar to a table of contents. We make an overview moc that will serve as the point-of-entry into all of the interconnected notes for this exercise (whereas a table of contents would list _all_ of your materials, organized hierarchically.) If you ctrl/cmd+click the wikilink above, Tangent will create the new note for you.

**Content for `met-planning-moc.md`:**

Copy this block (without the back-ticks and the word 'markdown') into that new note. Replace the text with your own thoughts as directed.

```markdown
# Met Collection DH Project Plan (MOC)

## 1. Focus Area & Research Question

*Define the specific subset of The Met's collection you will focus on (e.g., Department ID, date range, medium). Then, formulate a specific research question about this subset that you can explore using **one or more advanced DH techniques** covered in the tutorials (e.g., TF-IDF/Topic Modeling on descriptions, Network Analysis of artists/cultures, Geospatial Analysis of object origins, Visual Similarity analysis).*

**Focus Area:** [***Students specify their chosen subset. Example:*** *European Paintings (Department ID 11), 1800-1900*]

**My Question:** [***Students write their research question here. Example:*** *What are the dominant latent topics within the object descriptions for 19th-century European paintings in the collection?* OR *Example: Can visual similarity analysis identify distinct stylistic clusters within Japanese prints (Department ID 6) from the Edo period?* OR *Example: What does a network graph reveal about the co-occurrence of materials used in Arms and Armor (Department ID 4)?*]

## 2. Data Source

*   **Primary Data:** The Metropolitan Museum of Art Collection API ([https://metmuseum.github.io/](https://metmuseum.github.io/))
    *   `/objects` endpoint (for getting Object IDs within the focus area)
    *   `/objects/{objectID}` endpoint (for getting detailed metadata for selected objects)

## 3. Methodology Outline

*Outline the step-by-step process you will follow. **Wikilink relevant advanced DH tutorial notes** for the core analytical techniques. Be specific about data filtering and the chosen advanced method.*

**Steps:**

1.  [***Example Step:*** Query the `/objects` endpoint using appropriate parameters (e.g., `departmentIds`, `dateBegin`, `dateEnd`) to retrieve a list of Object IDs for the focus area.]
2.  [***Example Step:*** Handle potential pagination if the list of Object IDs is large [[DH Intermediate - 11 Handling API Pagination and Rate Limiting]] (though check Met API specifics - it might return all IDs at once for reasonable queries).]
3.  [***Example Step:*** Select a manageable subset of these Object IDs for detailed analysis (e.g., random sample, or first N results).]
4.  [***Example Step:*** Loop through the selected Object IDs, making requests to the `/objects/{objectID}` endpoint for each one. **Include pauses** (`time.sleep()`) between requests as good practice.]
5.  [***Example Step:*** Parse the JSON response for each object [[DH Intermediate - 09 Introduction to APIs and JSON]] and extract relevant data fields based on the research question (e.g., `title`, `objectName`, `artistDisplayName`, `objectDate`, `medium`, `objectURL`, `primaryImageSmall`, `department`, `culture`, `country`).]
6.  [***Example Step:*** Structure the collected data, likely using Pandas [[DH Intermediate - 01 Working with Tabular Data - Intro to Pandas]]. Handle missing or inconsistent data.]
7.  [***Example Step (if doing text analysis):*** Preprocess text fields (e.g., descriptions/titles) and apply TF-IDF [[DH Advanced - 01 Vector Space Models and TF-IDF]] or Topic Modeling [[DH Advanced - 02 Introduction to Topic Modeling with LDA]].]
8.  [***Example Step (if doing network analysis):*** Construct a network based on relationships (e.g., artist-medium, culture-period) using NetworkX [[DH Advanced - 04 Introduction to Network Analysis]] and analyze metrics [[DH Advanced - 05 Network Metrics and Community Detection]].]
9.  [***Example Step (if doing visual analysis):*** Download images for the subset [[DH Intermediate - 12 Fetching and Analyzing Content from API Results (OCR Text)]] (modify for images). Extract image features and calculate visual similarity [[DH Advanced - 10 Measuring Visual Similarity]]. *Note: This is computationally intensive.*]
10. [***Example Step (if doing geo analysis):*** Extract geographic information (country, culture - may need cleaning/mapping). Geocode place names [[DH Advanced - 06 Geocoding - Turning Place Names into Coordinates]] and map results [[DH Advanced - 07 Geospatial Data Analysis with GeoPandas]] & [[DH Advanced - 08 Interactive Mapping]].]
11. [***Example Step:*** Analyze the results generated by the chosen advanced technique.]
12. [***Example Step:*** Write up findings in `[[met-analysis.md]]`, incorporating insights from `[[Part 3: Readings Integration]]`.]
13. ... [*Students tailor steps precisely to their question and chosen method*] ...

## 4. Potential Challenges

*Identify anticipated difficulties related to the API, the data, or the chosen advanced method.*

*   [***Example:*** Inconsistent or sparse metadata in certain fields (e.g., precise object origin, detailed descriptions).]
*   [***Example:*** Handling the potentially large volume of data (Object IDs, metadata, images).]
*   [***Example:*** The computational cost and complexity of the chosen advanced method (e.g., topic modeling parameter tuning, image feature extraction).]
*   [***Example:*** Interpreting the output of complex models (e.g., topic models, network clusters, similarity scores).]
*   [***Example:*** Ensuring responsible use of image data (respecting Open Access terms).]
*   [***Students list potential challenges here.***]

## 5. Expected Outcome

*Describe what you hope to produce or learn.*

*   [***Example:*** A list of dominant topics derived from object descriptions and an analysis of how they relate to artistic movements of the period.]
*   [***Example:*** A network graph showing connections between materials and cultures, highlighting central elements.]
*   [***Example:*** Clusters of visually similar images, potentially revealing stylistic groupings not captured by metadata alone.]
*   [***Students describe their expected outcome here.***]

```

---

### Part 2: Coding Exercises

**Instructions:** Create the following notes. For each note, first read the objective and the partial code. In the "Prediction" section, write down what you **think** the code *should* do or output *before* you modify or run it. Then, complete the code as instructed, run the code block, and write your observations in the "Reflection" section, comparing the actual output to your prediction.

**Create Note:** [[coding-exercise-01-met-ids]]

Remember to add this to your MOC.

**Content:**

```markdown
# Coding Exercise 01: Fetching Met Object IDs

**Objective:** Use the Met API's `/objects` endpoint to fetch a list of Object IDs for a specific department.

**Prediction:**
[*Students write here: What Python data type do you expect `object_ids` to be? What kind of values (numbers, strings?) do you expect it to contain based on the API documentation? What might `total_objects` represent?*]

**Partial Code:**

NB that you need _three_ backticks to set off the code correctly so that Tangent will recognize it.

``python
import requests
import json

# Define the API endpoint for objects
objects_endpoint = "https://collectionapi.metmuseum.org/public/collection/v1/objects"

# Parameters: Find objects in the "Greek and Roman Art" department (Department ID 13)
params = {
    'departmentIds': # COMPLETE THIS LINE: provide the department ID as an integer
}

print(f"Fetching Object IDs for department {params['departmentIds']}...")
try:
    response = requests.get(objects_endpoint, params=params)
    response.raise_for_status() # Check for errors

    # Parse the JSON response
    data = response.# COMPLETE THIS LINE: parse the JSON response

    # Extract the total number of objects and the list of IDs
    # Check the API documentation for the exact keys!
    total_objects = data.# COMPLETE THIS LINE: get the key for total count
    object_ids = data.# COMPLETE THIS LINE: get the key for the list of IDs

    print(f"API returned a total of {total_objects} objects for this department.")
    if object_ids: # Check if the list is not empty or None
      print(f"Type of object_ids: {type(object_ids)}")
      print(f"Number of IDs received in this response: {len(object_ids)}")
      print(f"First 10 Object IDs: {object_ids[:10]}")
    else:
      print("No object IDs returned in the 'objectIDs' key or the key is missing.")


except requests.exceptions.RequestException as e:
    print(f"Request failed: {e}")
except json.JSONDecodeError:
    print(f"Failed to decode JSON response. Text: {response.text[:200]}")
except KeyError as e:
    print(f"Error accessing key in JSON response: {e}. Check API documentation for correct keys.")
    print(f"Response Data: {data}")
except Exception as e:
    print(f"An error occurred: {e}")

``

**Completion & Execution:**
[*Complete the code lines above, then run the code block. What happened? What errors or error messages did you encounter? Use Stackoverflow to try to find solutions; do not use ChatGPT or similar to fix the code (for what would be the point of that?)*]

**Reflection:**
[*Students write here: Did the output match your prediction regarding data type and content? Did the API return keys like 'total' and 'objectIDs'? Why is it important to check if `object_ids` is not empty/None before trying to access its elements or length?*]

```

---

**Create Note:** [[coding-exercise-02-met-object]]

Remember to add these wikilinks to your MOC.

**Content:**

```markdown
# Coding Exercise 02: Fetching Met Object Details

**Objective:** Use the Met API's `/objects/{objectID}` endpoint to fetch detailed metadata for a single object.

**Prediction:**
[*Students write here: What Python data type do you expect `object_data` to be? Based on exploring the Met website or API docs, what are 3-4 *specific keys* you might expect to find within the details for an object (e.g., 'title', 'artistDisplayName', 'primaryImageSmall')?*]

**Partial Code:**

NB that you need _three_ backticks to set off the code correctly so that Tangent will recognize it.

``python
import requests
import json

# Define the base URL part for individual objects
object_endpoint_base = "https://collectionapi.metmuseum.org/public/collection/v1/objects/"

# Use a specific Object ID (e.g., 436535 is Van Gogh's "Self-Portrait with a Straw Hat")
object_id = 436535

# Construct the full URL by appending the object_id
full_url = f"{object_endpoint_base}{object_id}" # Using an f-string

print(f"Fetching details for Object ID: {object_id} from {full_url}")

try:
    # Make the GET request for the specific object
    response = requests.# COMPLETE THIS LINE: make the GET request

    response.raise_for_status() # Check for errors

    # Parse the JSON response
    object_data = response.# COMPLETE THIS LINE: parse the JSON

    # Print the type and some expected fields (use .get() for safety)
    print(f"\nType of object_data: {type(object_data)}")

    obj_title = object_data.get(# COMPLETE THIS LINE: access 'title', provide default '')
    obj_artist = object_data.get(# COMPLETE THIS LINE: access 'artistDisplayName', provide default 'N/A')
    obj_medium = object_data.get(# COMPLETE THIS LINE: access 'medium', provide default '')
    obj_image_url = object_data.get(# COMPLETE THIS LINE: access 'primaryImageSmall', provide default '')

    print(f"\nTitle: {obj_title}")
    print(f"Artist: {obj_artist}")
    print(f"Medium: {obj_medium}")
    print(f"Small Image URL: {obj_image_url}")

    # Optional: Print all keys to see available data
    # if isinstance(object_data, dict):
    #    print(f"\nAll available keys: {list(object_data.keys())}")

except requests.exceptions.RequestException as e:
    print(f"Request failed: {e}")
except json.JSONDecodeError:
    print(f"Failed to decode JSON response. Text: {response.text[:200]}")
except Exception as e:
    print(f"An error occurred: {e}")

``

**Completion & Execution:**
[*Complete the code lines above, then run the code block.*]

**Reflection:**
[*Students write here: Did the `object_data` type match your prediction? Were the keys you predicted present in the response? What does using `.get('keyname', default_value)` do compared to `object_data['keyname']`? Why is `.get()` often safer when dealing with real-world API data?*]

```

---

**Create Note:** [[coding-exercise-03-advanced-snippet}}

Remember to add this note to your MOC.

**Content:**

```markdown
# Coding Exercise 03: Advanced Snippet (TF-IDF)

**Objective:** Apply `TfidfVectorizer` from `scikit-learn` to a small sample of text data (like object titles or mediums) to understand its basic usage.

**Prediction:**
[*Students write here: What is the purpose of `TfidfVectorizer`? What do you expect the `tfidf_matrix` variable to contain after the code runs? What might its dimensions (`.shape`) represent?*]

**Partial Code:**

NB that you need _three_ backticks to set off the code correctly so that Tangent will recognize it.

``python
from sklearn.feature_extraction.text import TfidfVectorizer
import pandas as pd

# Sample text data (e.g., object titles or mediums from the Met)
text_data = [
    "Oil on canvas",
    "Terracotta sculpture",
    "Oil painting on wood panel",
    "Limestone relief sculpture",
    "Oil on wood",
]
print("--- Input Text Data ---")
print(text_data)

# Initialize TfidfVectorizer (use default settings for this exercise)
vectorizer = # COMPLETE THIS LINE: create an instance of TfidfVectorizer

# Fit the vectorizer to the data and transform the data
# This learns the vocabulary and calculates TF-IDF scores
tfidf_matrix = vectorizer.# COMPLETE THIS LINE: use the correct method to fit and transform

# Get the feature names (vocabulary)
feature_names = vectorizer.get_feature_names_out()

# Display the results (converting sparse matrix to DataFrame for readability)
tfidf_df = pd.DataFrame(tfidf_matrix.toarray(), columns=feature_names)

print("\n--- TF-IDF Matrix ---")
print(tfidf_df)
print(f"\nMatrix shape: {tfidf_matrix.shape}") # (rows=documents, columns=vocabulary terms)

``

**Completion & Execution:**
[*Complete the code lines above, then run the code block.*]

**Reflection:**
[*Students write here: Did the matrix shape match your prediction? Look at the scores: why might "oil" have different scores in different rows? Why might "terracotta" or "limestone" have relatively high scores in their respective rows? How does this relate to the concepts of Term Frequency and Inverse Document Frequency?*]

```

---

### Part 3: Readings Integration

**Instructions:**

1.  Find 3 or 4 relevant scholarly articles or book chapters. These could relate to:
    *   The specific collection/department/period you chose at The Met.
    *   The application of your chosen advanced DH method (Topic Modeling, Network Analysis, Visual Similarity, etc.) to art history or cultural heritage collections.
    *   Methodological discussions about using museum APIs or computational methods in art historical research.
2.  For each reading, create a new note in your project folder (e.g., `reading-AuthorLastName-Year`).
3.  In each reading note, include: Full Citation, Summary (2-3 sentences), Relevance (1-2 sentences explaining connection to your project), (Optional) Key Quotes. *(Follow the structure shown in previous assessment examples)*. For quickly generating formatted bibliography, [https://zbib.org/](https://zbib.org/) is handy. (Nb - while not implemented here, software similar to Tangent like [Obsidian](https://obsidian.md) have many plugins that allow one to plumb Zotero directly into the note making workflow, and you may want to investigate those possibilities. A Tangent Workspace, since it saves all your work locally, _can also_ be opened by Obsidian!).

For reading notes, this structure might be useful:

```markdown
**NB** THIS IS A COMPLETELY FABRICATED REFERENCE. THE ACTUAL JDLS ONLY HAS ONE ISSUE  [which is available here.](https://journals.psu.edu/dls/issue/archive)

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

1.  Execute the analysis plan outlined in your [[met-planning-moc]]. Fetch data responsibly, structure it, apply your chosen advanced technique, and analyze the results. Save key outputs (datasets, visualizations, model results) within your project folder if possible.
2.  Create a final note named [[met-analysis]].
3.  In this note, present your findings.

**Create Note:** [[met-analysis]]

**Content for met-analysis:**

```markdown
# Analysis of [Your Research Question Topic] using The Met API

## Research Question & Focus Area

*Restate your research question and focus area from [[met-planning-moc]].*

[***Student writes question and focus area here.***]

## Method

*Briefly describe the steps taken, referencing your plan, specific API endpoints/parameters used, the chosen advanced technique(s) and relevant tutorials, and data handling.*

Following the plan in [[met-planning-moc]], Object IDs for [***Focus Area***] were retrieved using the Met API's `/objects` endpoint. Details for N objects were fetched via `/objects/{objectID}`. Relevant data fields ([***list key fields***]) were extracted and structured [***e.g., using Pandas***]. The core analysis involved [***Describe advanced method, e.g., applying LDA Topic Modeling [[DH Advanced - 02 Introduction to Topic Modeling with LDA]] to the 'objectName' field / calculating visual similarity using... [[DH Advanced - 10 Measuring Visual Similarity]] / constructing a network... [[DH Advanced - 04 Introduction to Network Analysis]]***].

*(Include key code snippets for the advanced analysis, summary tables, or visualizations)*

NB remember that code is set off using three backticks.

``python
# Example: Display top words for discovered topics
# topics = lda_model.print_topics(num_words=5)
# for topic in topics: print(topic)

# Example: Show head of DataFrame with similarity scores or network metrics
# print(results_df.head())
``

## Findings

*Present the main results derived from applying your chosen advanced technique.*

[***Students describe their findings here. Example:*** Topic modeling revealed X distinct thematic clusters in the descriptions, including Topic 1 (keywords: 'portrait', 'oil', 'canvas', 'woman'), Topic 2 ('landscape', 'panel', 'wood', 'river')... OR *Example:* Visual similarity analysis grouped images primarily by artist for well-known figures, but also identified clusters based on dominant color palettes for lesser-known works. OR *Example:* The network analysis showed 'Gold' and 'Iron' as highly central nodes in the materials network for Arms and Armor, connecting disparate object types...]

## Discussion

*Interpret your findings in relation to your research question and focus area. How do the computational results enhance understanding? **Connect your findings to the insights from your secondary readings using wikilinks.** *

The identified topics [***referencing findings***] suggest that [***Interpretation***]. This resonates with [[reading-Author1-Year]]'s discussion of [***relevant art historical context***]. The ability of [***advanced method used***] to [***specific capability, e.g., uncover latent themes / group by visual style***] provides a perspective potentially missed by traditional connoisseurship, as noted in the methodological discussion by [[reading-Author2-Year]]. However, the reliance on [***API field used, e.g., 'medium' field***] might oversimplify..., etc. [***Students adapt and expand, linking to their specific readings and findings.***]

## Limitations and Future Work

*Acknowledge limitations related to the API data (completeness, accuracy), the chosen subset, the specific advanced method's assumptions or parameters, and computational constraints. Suggest future directions.*

This analysis was limited by [***Example: the reliance on short 'title' fields rather than longer descriptions / the specific parameters chosen for the LDA model / the feature extraction method used for visual similarity***]. The subset analyzed might not represent the entire department. Future work could involve comparing results across different departments, fine-tuning the [***advanced method***] parameters, incorporating data from other museum APIs, or linking object data to external knowledge bases (e.g., artist biographies).
```
