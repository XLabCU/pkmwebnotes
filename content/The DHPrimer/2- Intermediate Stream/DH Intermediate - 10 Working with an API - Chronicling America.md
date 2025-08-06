---
title: "10: Working with an API - Chronicling America"
created: 2025-08-05T12:57:24.739Z
tags: [project]
---
# DH Intermediate - 10: Working with an API - Chronicling America

Let's put our knowledge of APIs and JSON into practice using a real-world example relevant to Digital Humanities: the **Chronicling America API** from the Library of Congress. This API provides access to metadata and content from digitized historical American newspapers (1777-1963).

**Chronicling America API:**

*   **Documentation:** It's essential to consult the official API documentation for details on endpoints, parameters, and usage: [https://chroniclingamerica.loc.gov/about/api/](https://chroniclingamerica.loc.gov/about/api/)
*   **No API Key Required:** This API is open and doesn't require registration or an API key, making it great for learning.
*   **Data Format:** Primarily returns data in JSON format.

**Goal:** Find newspaper pages published in Oregon containing the word "humanities" between 1910 and 1920.

**Steps:**

1.  Identify the correct API endpoint (based on documentation). For searching pages, it's `/search/pages/results/`.
2.  Determine the required and optional parameters for the search.
3.  Construct the request URL with parameters.
4.  Use the `requests` library to make the `GET` request.
5.  Check the response status.
6.  Parse the JSON response using `response.json()`.
7.  Extract and process the relevant information.

```python
import requests
import json
import time # To potentially add delays if making many requests

# 1. Define the base endpoint
base_url = "https://chroniclingamerica.loc.gov/search/pages/results/"

# 2. Define search parameters based on documentation & goal
# Reference: https://chroniclingamerica.loc.gov/search/pages/results/? K Wds=&andtext=&dateFilterType=yearRange&date1=1836&date2=1922&language=&sequence=0&lccn=&state=&rows=20&searchType=advanced&proxdistance=5&sort=date&Search=Search
# Key parameters for us:
#   state = "Oregon"
#   andtext = "humanities" (search term)
#   dateFilterType = "yearRange"
#   date1 = 1910 (start year)
#   date2 = 1920 (end year)
#   format = "json" (request JSON response)
#   rows = 100 (increase number of results per page, default is 20)

params = {
    'state': "Oregon",
    'andtext': "humanities",
    'dateFilterType': 'yearRange',
    'date1': 1910,
    'date2': 1920,
    'rows': 100, # Get more results per request
    'format': 'json'
}

try:
    # 3 & 4. Construct URL and make the GET request
    print("Making API request...")
    response = requests.get(base_url, params=params)
    response.raise_for_status() # Check for HTTP errors (4xx or 5xx)
    print(f"Request successful! Status Code: {response.status_code}")

    # 6. Parse the JSON response
    data = response.json()

    # 7. Process the data
    total_items = data.get('totalItems', 0)
    items_per_page = data.get('itemsPerPage', 0)
    start_index = data.get('startIndex', 0)
    print(f"\nFound {total_items} total matching pages.")
    print(f"Displaying items {start_index + 1} to {start_index + len(data.get('items', []))} on this page.")

    # Extract information about each matching page
    results = []
    if 'items' in data:
        for item in data['items']:
            page_info = {
                'lccn': item.get('lccn'), # Library of Congress Control Number (identifies the newspaper title)
                'date': item.get('date'),
                'title': item.get('title_normal'), # Normalized newspaper title
                'state': ", ".join(item.get('state', [])), # State(s) - it's a list
                'page': item.get('page'),
                'url': item.get('id') # This URL often points to the page view online
            }
            # Sometimes 'id' points to the OCR text file directly if ending in /ocr.json
            # Add a link to the OCR text if available (modify the url)
            if page_info['url'] and not page_info['url'].endswith('/ocr.json'):
                 page_info['ocr_url'] = page_info['url'] + "ocr.json"
            else:
                 page_info['ocr_url'] = None # or keep the url as is if it already points to json

            results.append(page_info)

    # Display the extracted information for the first few results
    print("\n--- Sample Results ---")
    for i, result in enumerate(results[:5]): # Show first 5
        print(f"\nResult {start_index + i + 1}:")
        print(f"  Title: {result['title']}")
        print(f"  Date: {result['date']}")
        print(f"  Page: {result['page']}")
        print(f"  State: {result['state']}")
        print(f"  LCCN: {result['lccn']}")
        print(f"  View URL: {result['url']}")
        if result['ocr_url']:
             print(f"  OCR Data URL: {result['ocr_url']}")


    # Optional: Save results to a file (e.g., CSV using pandas)
    if results:
        try:
            import pandas as pd
            df = pd.DataFrame(results)
            output_filename = "chronicling_america_humanities_oregon_1910-1920.csv"
            df.to_csv(output_filename, index=False, encoding='utf-8')
            print(f"\nSaved {len(results)} results to '{output_filename}'")
        except ImportError:
            print("\nPandas library not found. Skipping saving results to CSV.")
        except Exception as e_save:
            print(f"\nError saving to CSV: {e_save}")


    # Note: If total_items > items_per_page, you would need to make
    # additional requests, adjusting the 'page' parameter (or using 'startIndex')
    # to retrieve subsequent pages of results (pagination). This example
    # only retrieves the first page (up to 'rows' results).

except requests.exceptions.RequestException as e_req:
    print(f"Error during API request: {e_req}")
    if response:
      print(f"Response content: {response.text[:500]}...") # Show part of response if error
except json.JSONDecodeError as e_json:
    print(f"Error decoding JSON response: {e_json}")
    print(f"Response content: {response.text[:500]}...")
except Exception as e:
    print(f"An unexpected error occurred: {e}")

```

**Exploring Further:**

*   **Pagination:** Implement logic to retrieve all results if `totalItems` is greater than `itemsPerPage`. This typically involves making subsequent requests and incrementing a `page` parameter (check API docs for exact parameter name, often `page` or based on `startIndex`).
*   **Get OCR Text:** For pages found, you can use the `ocr_url` (if constructed correctly) to make another API request to fetch the actual OCR text content of the page.
*   **Analyze Results:** Use Pandas or NLTK on the collected data (e.g., analyze trends in mentions over time, examine the context using OCR text).
*   **Different Endpoints:** Explore other Chronicling America API endpoints (e.g., searching newspaper titles `/newspapers/`, getting issue details `/issues/`).

This example demonstrates a typical workflow for interacting with a DH-relevant API: understand the documentation, construct the request, handle the response, parse the JSON, and extract the needed information for further analysis or storage.

➡️ **Next Step:** [[DH Intermediate - 11 Handling API Pagination and Rate Limiting]] is what you need to get a handle on now.

---

*Back to [[Welcome to the DHP]] | [[DH Intermediate - Start Here]]*