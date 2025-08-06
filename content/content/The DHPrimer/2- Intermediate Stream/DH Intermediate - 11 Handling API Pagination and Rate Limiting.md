---
title: "11: Handling API Pagination and Rate Limiting"
created: 2025-08-05T12:57:24.739Z
tags: [project]
---
# DH Intermediate - 11: Handling API Pagination and Rate Limiting

In the previous example ([[DH Intermediate - 10 Working with an API - Chronicling America]]), we made a single request to the Chronicling America API. However, APIs often return large result sets split across multiple "pages". Our previous code only retrieved the *first* page of results (up to the `rows` limit we set). To get *all* matching results, we need to handle **pagination**.

Additionally, when making multiple requests, we must be mindful of **rate limiting** to avoid overwhelming the API server and potentially getting blocked.

**1. Understanding Pagination:**

APIs use different methods for pagination, but common patterns include:

*   **Page Number Parameter:** The API accepts a `page` parameter (e.g., `?page=1`, `?page=2`). You increment the page number in subsequent requests until you've retrieved all pages.
*   **Offset/Limit or Start Index/Rows:** The API uses parameters like `limit` (items per page) and `offset` (starting item number) or `rows` and `startIndex`. You adjust the offset/startIndex in subsequent requests (`offset = offset + limit` or `startIndex = startIndex + rows`).
*   **Cursor-Based:** The API response includes a `next_cursor` or `next_page_token`. You include this token in your next request to get the following batch of results.
*   **Link Headers:** Sometimes pagination information (like the URL for the next page) is included in the HTTP response headers (e.g., a `Link` header).

**Key Information in the Response:** You typically need to look for fields in the JSON response that tell you:
    *   Total number of results (`totalItems`, `total_count`, etc.).
    *   Number of items per page (`itemsPerPage`, `per_page`, `rows`, `limit`).
    *   Current page number or index (`currentPage`, `page`, `startIndex`).
    *   Sometimes, a direct URL or token for the next page (`next_page_url`, `next`).

**Chronicling America Pagination:**

Looking at the Chronicling America API response and documentation:
*   It uses `totalItems`, `itemsPerPage`, and `startIndex`.
*   We can calculate if more pages exist (`startIndex + itemsPerPage < totalItems`).
*   To get the next page, we need to make a new request with an updated `startIndex`. The next `startIndex` will be the current `startIndex + len(current_page_items)`.

**2. Implementing Pagination (Chronicling America Example):**

Let's modify the previous code to fetch *all* results for our search query, handling pagination.

```python
import requests
import json
import time
import pandas as pd # For collecting results

# --- Parameters and Setup --- (Same as before)
base_url = "https://chroniclingamerica.loc.gov/search/pages/results/"
search_term = "humanities" # Let's broaden the search slightly for more potential results
start_year = 1900
end_year = 1925
state_filter = "Oregon" # Keep state filter or remove for more results
results_per_page = 50 # How many results per API request

params = {
    'state': state_filter,
    'andtext': search_term,
    'dateFilterType': 'yearRange',
    'date1': start_year,
    'date2': end_year,
    'rows': results_per_page,
    'format': 'json',
    'page': 1 # Chronicling America seems to prefer 'page' for subsequent requests
               # We could also calculate startIndex, but 'page' might be simpler
}

all_results = []
current_page = 1
total_items = -1 # Initialize to -1, will be updated by first request

print(f"Starting search for '{search_term}' in {state_filter} ({start_year}-{end_year})...")

while True: # Loop until we break out
    params['page'] = current_page # Set the page number for the request

    try:
        print(f"Requesting page {current_page}...")
        response = requests.get(base_url, params=params)
        response.raise_for_status() # Check for HTTP errors

        data = response.json()

        # Get total items (only needs to be done once)
        if total_items == -1:
            total_items = data.get('totalItems', 0)
            items_per_page = data.get('itemsPerPage', results_per_page) # Use actual itemsPerPage if available
            print(f"Total items found: {total_items}")
            if total_items == 0:
                print("No results found.")
                break # Exit loop if no results

        # Process items on the current page
        current_page_items = data.get('items', [])
        if not current_page_items:
            print("No more items returned on this page, stopping.")
            break # Exit loop if no items on page

        print(f"  Processing {len(current_page_items)} items from page {current_page}...")
        for item in current_page_items:
            page_info = {
                'lccn': item.get('lccn'),
                'date': item.get('date'),
                'title': item.get('title_normal'),
                'state': ", ".join(item.get('state', [])),
                'page_text': item.get('page'), # Page number/text
                'url': item.get('id'),
                'ocr_url': item.get('id', '') + "ocr.json" if item.get('id') else None
            }
            all_results.append(page_info)

        # --- Pagination Logic ---
        # Calculate if we need to continue
        # Use len(all_results) as a reliable counter of processed items
        if len(all_results) >= total_items:
            print("All items retrieved.")
            break # Exit loop, we have everything

        # Prepare for the next page
        current_page += 1

        # --- Rate Limiting ---
        # PAUSE before the next request to be polite to the server
        sleep_time = 1.0 # Pause for 1 second (adjust as needed/documented)
        print(f"  Pausing for {sleep_time} second(s)...")
        time.sleep(sleep_time)


    except requests.exceptions.RequestException as e_req:
        print(f"Error during API request for page {current_page}: {e_req}")
        print("Stopping further requests.")
        break # Stop on error
    except json.JSONDecodeError as e_json:
        print(f"Error decoding JSON response for page {current_page}: {e_json}")
        print(f"Response content: {response.text[:500]}...")
        print("Stopping further requests.")
        break # Stop on error
    except Exception as e:
        print(f"An unexpected error occurred on page {current_page}: {e}")
        print("Stopping further requests.")
        break # Stop on error


print(f"\nFinished fetching results. Total results collected: {len(all_results)}")

# --- Save all collected results ---
if all_results:
    try:
        df = pd.DataFrame(all_results)
        output_filename = f"chronicling_america_{search_term}_{state_filter}_{start_year}-{end_year}_all.csv"
        df.to_csv(output_filename, index=False, encoding='utf-8')
        print(f"Saved {len(all_results)} results to '{output_filename}'")
    except Exception as e_save:
        print(f"\nError saving to CSV: {e_save}")

```

**3. Rate Limiting (`time.sleep()`):**

*   **Why:** Making rapid-fire requests can overload the API server, violate terms of service, and lead to temporary or permanent blocks.
*   **How:** The simplest method is to add a pause between requests using `time.sleep(seconds)`.
*   **Amount:** How long should you pause?
    *   **Check Documentation:** The API documentation is the BEST place to look for explicit rate limits (e.g., "max 10 requests per second" or "max 1000 requests per hour").
    *   **Be Conservative:** If no limits are stated, start with a conservative pause (e.g., 1 second) and increase it if you encounter errors (like HTTP 429 "Too Many Requests" or 503 "Service Unavailable").
    *   **Exponential Backoff:** For more robust error handling, you can implement exponential backoff: if a request fails due to rate limiting, wait 1 second, retry; if it fails again, wait 2 seconds, retry; then 4, 8, etc., up to a maximum.

**Key Takeaways:**

*   Always assume API results might be paginated.
*   Inspect API responses and documentation to understand the pagination method.
*   Implement loops to fetch subsequent pages until all data is retrieved or an error occurs.
*   **Crucially, include pauses (`time.sleep()`) between requests** to respect rate limits and be a good API citizen.

Handling pagination and rate limiting correctly is essential for reliably collecting complete datasets from APIs.

➡️ **Next Step:** Now that we can potentially collect many results (including URLs to OCR text), a logical next step could be [[DH Intermediate - 12 Fetching and Analyzing Content from API Results (OCR Text)]].

---

*Back to [[Welcome to the DHP]] | [[DH Intermediate - Start Here]]*