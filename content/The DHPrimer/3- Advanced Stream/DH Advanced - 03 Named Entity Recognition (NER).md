---
title: "03: Named Entity Recognition (NER)"
created: 2025-08-05T12:57:24.739Z
tags: [advanced]
---
# DH Advanced - 03: Named Entity Recognition (NER)

While topic modeling gives us a sense of the thematic content, **Named Entity Recognition (NER)** focuses on identifying and classifying specific named entities within text into pre-defined categories. These entities typically include:

*   **PERSON:** People, fictional characters.
*   **ORG:** Organizations, companies, institutions.
*   **GPE (Geopolitical Entity):** Countries, cities, states.
*   **LOC:** Non-GPE locations, mountain ranges, bodies of water.
*   **DATE:** Absolute or relative dates or periods.
*   **TIME:** Times smaller than a day.
*   **MONEY:** Monetary values, including units.
*   **PERCENT:** Percentage values.
*   **FAC (Facility):** Buildings, airports, highways, bridges.
*   **PRODUCT:** Objects, vehicles, foods, etc. (not services).
*   **EVENT:** Named hurricanes, battles, wars, sports events, etc.
*   **NORP (Nationalities or Religious or Political groups)**
*   *(Plus others, depending on the model)*

**Why is NER Useful in DH?**

NER transforms unstructured text into structured information by pinpointing key actors, places, and organizations. This enables:

*   **Knowledge Extraction:** Building databases or knowledge graphs about relationships between entities.
*   **Network Analysis:** Identifying nodes (e.g., people, organizations) for network construction based on co-occurrence or explicit relationships mentioned in text.
*   **Geospatial Analysis:** Extracting place names (GPE, LOC) for mapping and spatial analysis.
*   **Timeline Generation:** Extracting dates and events.
*   **Quantitative Analysis:** Counting mentions of specific entity types across different texts or time periods.

**Implementation with `spaCy`:**

While NLTK has NER capabilities, **`spaCy`** is often preferred for its speed, efficiency, excellent pre-trained models, and ease of use, especially for standard NER tasks.

**Installation:**

1.  Install spaCy: `pip install spacy`
2.  Download a pre-trained language model. Models vary in size and performance (e.g., `_sm`=small, `_md`=medium, `_lg`=large, `_trf`=transformer). Small models are faster but less accurate; larger models are slower but generally more accurate. Let's start with a small English model:
    `python -m spacy download en_core_web_sm`

**Basic NER Workflow:**

1.  Load a spaCy language model (`spacy.load()`). This object contains the processing pipeline, including the NER component.
2.  Process your text through the model (`nlp(text)`). This returns a `Doc` object.
3.  Iterate through the entities recognized in the `Doc` using `doc.ents`.
4.  Access entity properties like the text (`ent.text`) and the label (`ent.label_`).

**Example:**

```python
import spacy

# --- Load the spaCy model ---
# Use the model name you downloaded (e.g., 'en_core_web_sm')
model_name = 'en_core_web_sm'
try:
    nlp = spacy.load(model_name)
    print(f"Loaded spaCy model '{model_name}'")
except OSError:
    print(f"Error: spaCy model '{model_name}' not found.")
    print(f"Please run: python -m spacy download {model_name}")
    nlp = None # Set nlp to None to prevent errors later

# --- Sample Text ---
# Example from a historical context or literary analysis
text = """
In January 1813, Jane Austen published Pride and Prejudice while living near Alton.
The novel, initially proposed to the London publisher Thomas Cadell, became immensely popular.
Later works like Emma followed, cementing Austen's reputation across England and eventually the world.
Estimates suggest early editions cost around 15 shillings, roughly 50 pounds today according to the Bank of England.
Austen visited Box Hill, a famous Surrey landmark, which inspired a key scene in Emma.
"""

# --- Process Text and Extract Entities ---
if nlp:
    doc = nlp(text) # Process the text with the loaded model

    print("\n--- Identified Entities ---")
    print(f"{'Entity Text':<25} | {'Label':<10} | {'Explanation'}")
    print("-" * 65)
    entity_found = False
    for ent in doc.ents:
        entity_found = True
        print(f"{ent.text:<25} | {ent.label_:<10} | {spacy.explain(ent.label_)}")

    if not entity_found:
        print("No entities found in the text with this model.")

else:
    print("\nspaCy model not loaded. Cannot perform NER.")

```

**Visualizing Entities:**

`spaCy` includes a great visualizer called `displacy`.

```python
from spacy import displacy

# --- Visualize Entities Inline ---
# Run this in an environment that can render HTML (like Jupyter Notebook/Lab)
if nlp and doc.ents: # Check if nlp loaded and entities were found
    print("\n--- Visualizing Entities ---")
    # style='ent' highlights named entities
    html = displacy.render(doc, style="ent", jupyter=False) # jupyter=False returns HTML string

    # To display in environments that support HTML:
    from IPython.display import HTML, display
    display(HTML(html))
    # If not in IPython, you could save 'html' to a file:
    # with open("ner_visualization.html", "w", encoding="utf-8") as f:
    #     f.write(html)
    # print("Saved visualization to ner_visualization.html")

elif nlp:
     print("\nNo entities found to visualize.")
else:
    print("\nspaCy model not loaded. Cannot visualize entities.")

```

**Filtering and Using Entities:**

You can easily filter entities by their label and use the extracted information.

```python
if nlp and doc.ents:
    print("\n--- Extracting Specific Entity Types ---")

    people = [ent.text for ent in doc.ents if ent.label_ == "PERSON"]
    locations = [ent.text for ent in doc.ents if ent.label_ in ["GPE", "LOC"]] # GPE=GeoPolitical, LOC=Location
    dates = [ent.text for ent in doc.ents if ent.label_ == "DATE"]
    orgs = [ent.text for ent in doc.ents if ent.label_ == "ORG"]

    print(f"People found: {people}")
    print(f"Locations/GPE found: {locations}")
    print(f"Dates found: {dates}")
    print(f"Organizations found: {orgs}")

    # Example Use Case: Find sentences mentioning a specific person
    target_person = "Austen"
    print(f"\n--- Sentences mentioning '{target_person}' ---")
    for sent in doc.sents: # Iterate through sentences
        if target_person in sent.text:
            # Further check if the person entity is actually in this sentence
            if any(ent.text == target_person and ent.label_ == "PERSON" for ent in sent.ents):
                 print(f"-> {sent.text.strip()}")

else:
     print("\nCannot extract specific entities: spaCy model not loaded or no entities found.")

```

**Considerations:**

*   **Model Choice:** Larger models (`_md`, `_lg`, `_trf`) generally offer better accuracy but require more memory and processing time. Transformer (`_trf`) models are often state-of-the-art but significantly slower.
*   **Domain Adaptation:** Models trained primarily on news text might make errors on historical, literary, or specialized scientific text. Fine-tuning or training custom models might be necessary for high accuracy in specific domains.
*   **Ambiguity:** NER systems can struggle with ambiguous names (e.g., "Washington" - the person, state, or city?). Context is key.
*   **Nested Entities:** Standard NER models usually don't handle nested entities well (e.g., "Bank of England" is an ORG, "England" within it is a GPE).

Named Entity Recognition is a powerful tool for extracting structured information from text, serving as a vital input for many downstream DH tasks like network construction, mapping, and building knowledge bases.

➡️ **Next Step:** [[DH Advanced - 04 Introduction to Network Analysis]]

---

*Back to [[Welcome to the DHP]] | [[DH Advanced - Start Here]]*