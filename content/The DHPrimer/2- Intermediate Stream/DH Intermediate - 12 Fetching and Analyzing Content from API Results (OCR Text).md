---
title: "12: Fetching and Analyzing Content from API Results (OCR Text)"
created: 2025-08-05T12:57:24.739Z
tags: [project]
---
# DH Intermediate - 12: Fetching and Analyzing Content from API Results (OCR Text)

In the previous notebooks, we used the Chronicling America API to search for newspaper pages matching certain criteria and collected metadata about those pages, including URLs pointing to their Optical Character Recognition (OCR) text data.

Now, we'll take the next step: fetching the actual text content from those OCR URLs and performing some basic analysis on it. This moves us from metadata to the textual substance of the historical documents.

**Goal:**

1.  Load the results saved from the previous pagination example.
2.  Iterate through the results, fetching the OCR text for each page using its `ocr_url`.
3.  Extract the plain text from the OCR data (which Chronicling America provides in JSON).
4.  Perform simple analysis (e.g., word count, check for specific keywords) on the fetched text.
5.  Store the fetched text and analysis results alongside the original metadata.

**Important Considerations:**

*   **Rate Limiting:** Fetching the content for *each* result means making many individual API requests. **Respecting rate limits (`time.sleep()`) is absolutely critical here.**
*   **Data Volume:** OCR text can be substantial. Fetching and storing text for thousands of pages can consume significant time, bandwidth, and disk space. We might process only a subset for demonstration.
*   **OCR Quality:** Remember that OCR quality varies. The text might contain errors, strange characters, or formatting issues.

**Setup:**

Load the results from the CSV file created in the previous step ([[DH Intermediate - 11 Handling API Pagination and Rate Limiting]]).

```python
import requests
import json
import time
import pandas as pd
# May need nltk for analysis later
# import nltk
# from nltk.tokenize import word_tokenize

# --- Configuration ---
# Use the filename saved in the previous step
input_filename = "chronicling_america_humanities_Oregon_1900-1925_all.csv"
output_filename = "chronicling_america_humanities_Oregon_1900-1925_with_ocr.csv"
# Limit the number of pages to process in this example (to save time)
# Set to None to process all rows in the input file
MAX_PAGES_TO_PROCESS = 10
# Pause between OCR requests (in seconds) - BE POLITE!
SLEEP_TIME_OCR = 1.5

# --- Load Previous Results ---
try:
    df = pd.read_csv(input_filename)
    print(f"Loaded {len(df)} results from '{input_filename}'")
    # Ensure the ocr_url column exists
    if 'ocr_url' not in df.columns:
        raise ValueError("Input CSV must contain an 'ocr_url' column.")
    # Handle potential missing values in ocr_url more robustly
    df['ocr_url'] = df['ocr_url'].fillna('')
except FileNotFoundError:
    print(f"Error: Input file '{input_filename}' not found.")
    print("Please run the previous notebook (DH Intermediate - 11) first to generate it.")
    df = None # Set df to None to prevent errors later
except Exception as e:
    print(f"Error loading CSV: {e}")
    df = None

```

**Fetching and Processing OCR Text:**

We will iterate through the DataFrame rows (up to our limit), make requests to the `ocr_url`, parse the JSON, extract the text, and perform basic analysis.

```python
if df is not None:
    ocr_texts = []
    ocr_word_counts = []
    processed_count = 0

    print(f"\nStarting OCR fetching process (max {MAX_PAGES_TO_PROCESS or 'all'} pages)...")

    # Iterate through DataFrame rows
    # Use df.iterrows() which yields index and row (as a Series)
    for index, row in df.iterrows():
        # Stop if we reach the processing limit
        if MAX_PAGES_TO_PROCESS is not None and processed_count >= MAX_PAGES_TO_PROCESS:
            print(f"\nReached processing limit of {MAX_PAGES_TO_PROCESS} pages.")
            break

        ocr_url = row['ocr_url']
        page_id_for_msg = f"LCCN {row['lccn']}, Date {row['date']}, Page {row['page_text']}" # For logging

        # Initialize values for this row
        current_ocr_text = None
        current_word_count = 0

        # Check if URL is valid before making request
        if isinstance(ocr_url, str) and ocr_url.startswith('http'):
            print(f"Processing {processed_count + 1}: Fetching OCR for {page_id_for_msg} from {ocr_url}")
            try:
                # Make the API request for OCR data
                response = requests.get(ocr_url)
                response.raise_for_status() # Check for HTTP errors

                # Chronicling America OCR URLs ending in .json return JSON
                ocr_data = response.json()

                # Extract the actual text (assuming key 'ocr_eng', check if API changes)
                # Use .get() to avoid KeyError if the key is missing
                current_ocr_text = ocr_data.get('ocr_eng') # Or potentially another key like 'text'

                if current_ocr_text:
                    # Basic Analysis: Word Count
                    # Simple split on whitespace for word count
                    current_word_count = len(current_ocr_text.split())
                    print(f"  -> Success! Fetched text, word count: {current_word_count}")
                else:
                    print(f"  -> Warning: OCR URL successful, but no text found in key 'ocr_eng'.")
                    current_ocr_text = "" # Use empty string if no text found

                # --- Rate Limiting ---
                print(f"  Pausing for {SLEEP_TIME_OCR} seconds...")
                time.sleep(SLEEP_TIME_OCR)

            except requests.exceptions.RequestException as e_req:
                print(f"  -> Error fetching OCR from {ocr_url}: {e_req}")
                current_ocr_text = f"Error: {e_req}" # Store error message
            except json.JSONDecodeError as e_json:
                print(f"  -> Error decoding JSON from {ocr_url}: {e_json}")
                current_ocr_text = f"Error: JSONDecodeError" # Store error message
            except Exception as e:
                print(f"  -> An unexpected error occurred for {ocr_url}: {e}")
                current_ocr_text = f"Error: {e}" # Store error message
        else:
            print(f"Processing {processed_count + 1}: Skipping invalid or missing OCR URL for {page_id_for_msg}")
            current_ocr_text = "Error: Invalid URL"

        # Append results for this row (even if errors occurred)
        ocr_texts.append(current_ocr_text)
        ocr_word_counts.append(current_word_count)
        processed_count += 1

    # --- Add fetched data back to the DataFrame ---
    # Only add data for the rows processed
    if processed_count > 0:
      # Use slicing to match the number of processed rows
      df_processed_slice = df.iloc[:processed_count].copy() # Work on a copy of the slice

      # Add new columns
      df_processed_slice['ocr_text'] = ocr_texts
      df_processed_slice['ocr_word_count'] = ocr_word_counts

      print(f"\nAdded OCR text and word counts for {processed_count} rows.")

      # --- Save the updated DataFrame ---
      try:
          df_processed_slice.to_csv(output_filename, index=False, encoding='utf-8')
          print(f"Successfully saved updated data to '{output_filename}'")
      except Exception as e_save:
          print(f"\nError saving updated data to CSV: {e_save}")
    else:
       print("\nNo pages were processed, skipping saving.")

else:
    print("\nSkipping OCR fetching because initial DataFrame failed to load.")

```

**Exploring the Results:**

Now you have a new CSV file (`chronicling_america_humanities_oregon_1900-1925_with_ocr.csv` or similar) that contains not only the metadata but also the actual OCR text and a basic word count for each processed newspaper page.

You can load this richer dataset into Pandas and perform more in-depth analysis:

*   Use NLTK (tokenization, stop word removal, frequency distributions, POS tagging, lemmatization) on the `ocr_text` column.
*   Search for specific names, places, or concepts within the text.
*   Analyze sentiment or topics across different newspapers or time periods (more advanced NLP).
*   Clean the OCR text further (removing common OCR errors, standardizing formatting).

**Key Takeaway:**

This notebook demonstrates how to bridge the gap between metadata discovery (finding relevant documents via API search) and content analysis (fetching and working with the actual text of those documents). Remember that patience and careful rate limiting are essential when programmatically accessing content for numerous items.

➡️ **Next Step:** 


---

*Back to [[Welcome to the DHP]] | [[DH Intermediate - Start Here]]*