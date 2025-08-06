---
title: "Formative Assessment: Analyzing Frankenstein"
created: 2025-08-05T12:57:24.739Z
tags: [beginner]
---
## Formative Assessment: Analyzing Frankenstein

**Objective:** To apply the foundational Python and Digital Humanities concepts learned in the Introductory stream of the DH Primer tutorials to formulate a research question about Mary Shelley's *Frankenstein*, devise an analytical plan, execute parts of that plan using code, and integrate findings with secondary readings.

**Environment:** Tangent Noteboook (or similar interactive markdown editor)

**Overall Instructions for Students:**

1.  **Create a Project Folder:** In Tangent, create a new **Folder** (not a Workspace) named `Frankenstein_DH_Project`. All notes related to this assessment should be saved within this folder.
2.  **Obtain the Data:** Download the plain text version of Mary Shelley's *Frankenstein* (the 1818 version is common) from Project Gutenberg: [https://www.gutenberg.org/ebooks/84](https://www.gutenberg.org/ebooks/84). Save the plain text file (`.txt`) inside your `Frankenstein_DH_Project` folder (e.g., name it `frankenstein_text.txt`).
3.  **Complete the Parts Below:** Work through the following parts by creating and editing the specified markdown notes within your project folder. Pay close attention to instructions regarding wikilinking and code execution.

**Tip** if you're not sure _where_ Tangent is saving your materials (where is your workspace?) you can run this python snippet to find out:

```python
import os
print(f"Current working directory: \n {os.getcwd()}")
```

**Why Frankenstein?**

Why not? It is _the_ text exploring technological/scientific ambition and the consequences thereof. If it isn't the perfect text for exploring DH, I don't know what is.

---

### Part 1: Planning Your Project

**Create Note:** [[intro-stream-planning-moc]]

A 'MOC' is a 'map-of-content'; you can think of this as being similar to a table of contents. We make an overview moc that will serve as the point-of-entry into all of the interconnected notes for this exercise (whereas a table of contents would list _all_ of your materials, organized hierarchically.) If you ctrl/cmd+click the wikilink above, Tangent will create the new note for you.

**Content for [[intro-stream-planning-moc]]:**

```markdown
# Frankenstein DH Project Plan (MOC)

## 1. Research Question

*Formulate a specific research question about Mary Shelley's *Frankenstein* that you think can be explored using the DH techniques learned so far (text cleaning, frequency analysis, potentially basic NER or data structuring if covered). Your question should be answerable using computation on the text.*

**My Question:** [***Students write their research question here.*** *Example: How does the frequency of words related to "creation" and "destruction" change across the major sections of Frankenstein?*]

## 2. Data Source

*   **Primary Text:** `[[frankenstein_text]]` (Check that this file is in your workspace folder; it is sourced from [The Gutenberg Project](https://gutenberg.org/ebooks/84). Also provide a correct citation for this _particular_ version of the text..)

## 3. Methodology Outline

*Outline the step-by-step process you will follow to address your research question. For each step involving a technique covered in the tutorials, **add a wikilink** to the relevant tutorial note.*

**Steps:**

1.  [***Example Step:*** Read the entire text file into Python [[DH Beginner - 06 Reading Text Files]]]
2.  [***Example Step:*** Preprocess the text: convert to lowercase, remove punctuation [[DH Beginner - 09 Basic Text Cleaning - Removing Punctuation]]]
3.  [***Example Step:*** Tokenize the text and remove standard English stop words [[DH Intermediate - 05 Text Analysis with NLTK - Tokenization and Stop Words]]]
4.  [***Example Step:*** Define lists of keywords related to "creation" (e.g., create, make, form, birth...) and "destruction" (e.g., destroy, kill, ruin, decay...).]
5.  [***Example Step:*** Divide the processed text into sections (e.g., Volume I, II, III - requires identifying markers in the text or approximate word counts).]
6.  [***Example Step:*** For each section, calculate the frequency of the defined keywords [[DH Beginner - 10 Counting Word Frequencies]] or [[DH Intermediate - 06 Frequency Distributions and Concordancing with NLTK]].]
7.  [***Example Step:*** Compare the relative frequencies across sections.]
8.  [***Example Step:*** Write up findings in [[frankenstein-analysis.md]], incorporating insights from [[Part 3: Readings Integration]].]
9.  ... [*Students add/modify steps based on their question*] ...

## 4. Potential Challenges

*Identify any anticipated difficulties.*

*   [***Example:*** Accurately dividing the text into meaningful sections programmatically.]
*   [***Example:*** Defining comprehensive keyword lists.]
*   [***Example:*** OCR errors in the source text, if any.]
*   [***Students list potential challenges here.***]

## 5. Expected Outcome

*Describe what you hope to produce or learn.*

*   [***Example:*** A quantitative comparison showing whether destructive language becomes more prevalent later in the novel.]
*   [***Students describe their expected outcome here.***]

```

---

### Part 2: Coding Exercises

**Instructions:** Create the following notes. For each note, first read the objective and the partial code. In the "Prediction" section, write down what you **think** the code *should* do or output *before* you modify or run it. Then, complete the code as instructed, run the code block, and write your observations in the "Reflection" section, comparing the actual output to your prediction.

**Create Note:** [[coding-exercise-01-reading]]

Remember to add these wikilinks to your MOC.

**Content:**

```markdown
# Coding Exercise 01: Reading the Text

**Objective:** Read the first 500 characters of the *Frankenstein* text file.

**Prediction:**
[*Students write here: What do you expect the `print()` statements to output? What type will `text_snippet` be?*]

**Partial Code:**

```python
# Make sure 'frankenstein_text.md' is in the same folder!
filename = "frankenstein_text.md"
characters_to_read =  # COMPLETE THIS LINE: define how many characters to read

try:
    # Use 'with open' to ensure the file is closed properly
    # Specify 'r' mode for reading and 'utf-8' encoding
    with open(filename, 'r', encoding='utf-8') as # COMPLETE THIS LINE: assign file object to 'f'
        # Read only the specified number of characters
        text_snippet = f. # COMPLETE THIS LINE: use the correct read method

    print(f"--- First {characters_to_read} characters ---")
    print(text_snippet)
    print("\n--- End of snippet ---")
    print(f"Type of text_snippet: {type(text_snippet)}") # Check the data type

except FileNotFoundError:
    print(f"Error: The file '{filename}' was not found in this folder.")
except Exception as e:
    print(f"An error occurred: {e}")

```

**Completion & Execution:**
[*Complete the code lines above, then run the code block using Ctrl+Enter or Cmd+Enter.*]

**Reflection:**
[*Students write here: Did the output match your prediction? Were there any surprises? What did you learn or reinforce?*]

---

**Create Note:** [[coding-exercise-02-cleaning]]

Remember to add these wikilinks to your MOC.

**Content:**

```markdown
# Coding Exercise 02: Basic Cleaning

**Objective:** Take a sample sentence, convert it to lowercase, and remove punctuation using string methods.

**Prediction:**
[*Students write here: What will the `cleaned_sentence` look like after the code runs?*]

**Partial Code:**

NB that you need _three_ backticks to set off the code correctly so that Tangent will recognize it.

``python
import string # Import the string module to access punctuation

sentence = "It was indeed a deplorable Hegira, my dear sister, that!"
print(f"Original: '{sentence}'")

# Convert to lowercase
lower_sentence = sentence. # COMPLETE THIS LINE: call the lowercase method

# Initialize cleaned sentence with the lowercase version
cleaned_sentence = lower_sentence

# Iterate through all punctuation characters provided by string.punctuation
for punc_char in string. # COMPLETE THIS LINE: access the punctuation constant
    # Replace each punctuation character with an empty string
    cleaned_sentence = cleaned_sentence. # COMPLETE THIS LINE: use replace() correctly

print(f"Cleaned:  '{cleaned_sentence}'")
``

**Completion & Execution:**
[*Complete the code lines above, then run the code block.*]

**Reflection:**
[*Students write here: Did the cleaning work as expected? What punctuation was removed? Did it match your prediction?*]

```

**Create Note:** [[coding-exercise-03-frequency]]

Remember to add these wikilinks to your MOC.

**Content:**

```markdown
# Coding Exercise 03: Word Frequency

**Objective:** Calculate the frequency of words in a given list of tokens using a dictionary.

**Prediction:**
[*Students write here: What key-value pairs do you expect to see in the `word_counts` dictionary at the end?*]

**Partial Code:**

NB that you need _three_ backticks to set off the code correctly so that Tangent will recognize it.

```python
tokens = ['monster', 'creature', 'monster', 'science', 'life', 'life', 'monster', 'adam', 'creature']
print(f"Tokens: {tokens}")

# Initialize an empty dictionary to store word counts
word_counts = # COMPLETE THIS LINE: create an empty dictionary

# Loop through each token in the list
for token in tokens:
    # Check if the token is already a key in the dictionary
    if token in word_counts:
        # If yes, increment its count
        word_counts[token] # COMPLETE THIS LINE: increment the count
    else:
        # If no, add it to the dictionary with a count of 1
        word_counts[token] = # COMPLETE THIS LINE: set the initial count

print("\n--- Word Counts ---")
print(word_counts)

# Bonus: Print the count for 'monster'
if 'monster' in word_counts:
    print(f"\nCount of 'monster': {word_counts['monster']}")
``

**Completion & Execution:**
[*Complete the code lines above, then run the code block.*]

**Reflection:**
[*Students write here: Did the final `word_counts` dictionary match your prediction? Explain how the `if/else` block worked to count the words.*]
```

What other things do you need to do? Drawing from the relevant tutorial, make more coding practice notes as necessary.

### Part 3: Readings Integration

**Instructions:**

1.  Find 1 or 2 relevant scholarly articles or book chapters that discuss *Frankenstein* from a perspective related to your research question OR that discuss the Digital Humanities methods you plan to use.
2.  For each reading, create a new note in your project folder. Name the note clearly (e.g., `reading-AuthorLastName-Year.md`).
3.  In each reading note, include the following:
    *   **Full Citation:** (APA, MLA, Chicago, etc. - be consistent).
    *   **Summary:** 2-3 sentences summarizing the main argument in your own words.
    *   **Relevance:** 1-2 sentences explaining how this reading connects to *your* specific project question or methods.
    *   **(Optional) Key Quotes:** Any direct quotes you might want to use later.

**Example Note:** `reading-Smith-2020.md`

```markdown
#NB THIS IS A COMPLETELY FABRICATED REFERENCE. THE ACTUAL JDLS ONLY HAS ONE ISSUE 
[which is available here.](https://journals.psu.edu/dls/issue/archive)

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

## Advice on writing useful summaries and relevance

The following comes from [Professor Chad Black](https://chadblack.net/2023SPORT/resources/how%20to%20write%20a%20precis%20and%20memo/) and you might find it useful:


Sentence one gives the following information:
        - name of the author, title of the work, date in parenthesis;
        an intentionally chosen active verb (argues, asserts, claims, denies, refutes, proves, disproves, explains, etc.);
        - a that clause containing the major claim (thesis) of the work.

Sentence two gives an explanation of how the author develops and supports the major claim of the work identified in the first sentence.
    
Sentence three states the author’s apparent purpose, followed by an “in order to” phrase.

_Formula:_

    In Chapter 1 of One Dimensional Man (1964), Herbert Marcuse argues that [...]. To build this argument, Marcuse [...]. Marcuse makes this argument in order to [...].

Here's an example from Daniel Nemser's Infrastructures of Race: Concentration and Biopolitics in Colonial Mexico (Austin: University of Texas Press, 2017):

    In Infrastructures of Race (2017), Daniel Nemser argues that colonial Spanish America created modern racialization by spatializing race as an infrastructure of domination and accumulation. To build this argument, Nemser draws on Foucault's biopolitical regime and Lefebvre's production of space and territorializing of domination, and divides his analysis into four chapters that move through the colonial period through a succession of material, spatial practices: congregación (concentration), recogimiento (enclosure), seperación (segregated districts), and colección. Nemser does this in order to reorient the social construction of race away from bodily difference and to material space and domination, as a means of overcoming the naturalization of difference as a thing upon which race is projected.


---

### Part 4: Analysis and Write-up

**Instructions:**

1.  Execute the analysis plan outlined in your [[planning-moc]]. You can add new code blocks directly into your MOC or create separate analysis script notes if needed.
2.  Create a final note named [[frankenstein-analysis]].
3.  In this note, present your findings.

**Create Note:** [[frankenstein-analysis]]`

**Content for frankenstein-analysis:**

```markdown
# Analysis of [Your Research Question Topic] in Frankenstein

## Research Question

*Restate your research question from [[planning-moc.md]].*

[***Student writes question here.***]

## Method

*Briefly describe the steps you took, referencing your plan and tutorials.*

Following the plan outlined in [[planning-moc.md]], the text `[[frankenstein_text.txt]]` was processed using techniques including file reading [[DH Beginner - 06 Reading Text Files]], text cleaning [[DH Beginner - 09 Basic Text Cleaning - Removing Punctuation]], and word frequency counting [[DH Beginner - 10 Counting Word Frequencies]]. [***Students adapt this based on their specific methods.***]

*(Include snippets of code output or visualizations if relevant and possible in your environment; remember that you need three backticks on a code block)*

``python
# Example: Snippet of final frequency results (if applicable)
# section_frequencies = {'Vol1': {'create': 5, 'destroy': 2}, 'Vol2': {'create': 3, 'destroy': 8}}
# print(section_frequencies)
``

## Findings

*Present the main results of your analysis.*

[***Students describe their findings here. Example:*** The analysis revealed a marked increase in the relative frequency of "destruction" keywords in Volume III compared to Volume I. Words like 'kill' and 'ruin' appeared X times more often per 1000 words in the final section.]

## Discussion

*Interpret your findings. What do they suggest about the novel or your research question? **Crucially, connect your findings to the insights from your secondary readings using wikilinks.** *

e.g.,

The quantitative results support the idea that the novel's language mirrors its thematic trajectory. The shift towards destructive terminology in the later stages aligns with critical interpretations like [[reading-Smith-2020]] which argues for the narrative's linguistic reflection of its tragic arc. Furthermore, the specific choice of words like '...' might relate to [***Student discusses another reading, e.g., [[reading-Jones-2018]]'s points about romantic notions of decay...***].

## Limitations and Future Work

*Acknowledge any limitations of your analysis and suggest potential next steps.*

e.g.,

This analysis was limited by [***Example: a simple keyword list, not accounting for synonyms or context***]. Future work could involve [[DH Advanced - 07 Stemming and Lemmatization with NLTK]] to group word forms or using [[DH Advanced - 02 Introduction to Topic Modeling with LDA]] to identify broader thematic clusters beyond predefined keywords.

```


