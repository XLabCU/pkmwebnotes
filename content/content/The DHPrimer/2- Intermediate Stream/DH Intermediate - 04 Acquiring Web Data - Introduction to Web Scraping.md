---
title: "04: Acquiring Web Data - Introduction to Web Scraping"
created: 2025-08-05T12:57:24.739Z
tags: [project]
---
# DH Intermediate - 04: Acquiring Web Data - Introduction to Web Scraping

Sometimes, the data you need isn't neatly packaged in a downloadable file. It might be spread across web pages. **Web scraping** is the process of automatically extracting information from websites.

**Important Considerations:**

*   **Ethics and Legality:** ALWAYS check a website's `robots.txt` file (e.g., `www.example.com/robots.txt`) and its Terms of Service before scraping. Respect the rules; some sites prohibit scraping.
*   **Rate Limiting:** Don't overload a website's server. Make requests at a reasonable rate (e.g., add pauses using `time.sleep()`). Excessive requests can get your IP address blocked.
*   **APIs First:** If a website offers an API (Application Programming Interface), use that instead of scraping. APIs provide structured data access and are generally preferred.
*   **Website Structure Changes:** Scrapers are often fragile. If the website's HTML structure changes, your scraper might break.

**Core Libraries:**

1.  **`requests`**: Used to send HTTP requests (like GET requests to fetch a web page's content).
2.  **`beautifulsoup4` (BS4)**: Used to parse the HTML content received from `requests` and navigate the HTML structure to find the data you want.

**Installation:**

If not already installed:
`pip install requests beautifulsoup4 lxml`
*(lxml is a recommended parser for BeautifulSoup)*

**Basic Scraping Workflow:**

1.  **Inspect:** Use your web browser's developer tools (usually F12 or right-click -> Inspect) to examine the HTML structure of the page and identify the tags and attributes containing the data you need.
2.  **Fetch:** Use `requests.get(url)` to download the HTML content.
3.  **Parse:** Use `BeautifulSoup(html_content, 'lxml')` to create a parseable object.
4.  **Extract:** Use BeautifulSoup methods like `find()`, `find_all()`, and CSS selectors (`select()`) to locate and extract the desired data.
5.  **Store/Process:** Save the extracted data (e.g., to a list, dictionary, DataFrame, or file).

**Example: Scraping Book Titles from Project Gutenberg's "Top 100 Ebooks Yesterday"**

*(Note: Project Gutenberg is generally permissive, but always be mindful. This is for educational purposes. Website structure can change.)*

```python
import requests
from bs4 import BeautifulSoup
import time # To add delays

# URL of the page we want to scrape
url = "https://www.gutenberg.org/browse/scores/top"

try:
    # 2. Fetch the HTML content
    print(f"Fetching URL: {url}")
    # Add headers to mimic a browser request (sometimes helpful)
    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3'}
    response = requests.get(url, headers=headers)
    response.raise_for_status() # Raise an exception for bad status codes (like 404, 500)
    print("Fetch successful!")

    # 3. Parse the HTML
    # We use 'lxml' as the parser
    soup = BeautifulSoup(response.content, 'lxml')
    # print(soup.prettify()) # Uncomment to see the full HTML structure

    # 4. Extract the data
    # Inspecting the page (using browser tools) reveals titles are often within <li><a> tags.
    # Let's find the main list container first. Inspection might show an <ol> tag.
    # We'll refine the selector based on actual inspection. Let's assume titles are inside <a> tags within list items <li>.
    # A more specific selector might be needed if the structure is complex.
    # Let's try finding all <a> tags within <li> tags - this might need adjustment!
    # Update (based on inspecting Gutenberg): Titles seem to be inside <a> tags within <li> elements.
    
    top_books_list = []
    # Find all list items <li> first
    list_items = soup.find_all('li') 
    
    print(f"\nFound {len(list_items)} list items. Attempting to extract book titles...")

    count = 0
    for item in list_items:
        # Find the first <a> tag within this list item
        link = item.find('a')
        if link and link.has_attr('href') and "/ebooks/" in link['href']:
            # Extract text, clean whitespace, and potentially remove download count like "(123)"
            title_text = link.text.strip()
            # Simple cleaning: remove potential trailing counts like ' (nnn)'
            if '(' in title_text and title_text.endswith(')'):
                 title_text = title_text.rsplit(' (', 1)[0]

            if title_text: # Ensure it's not empty after cleaning
                 top_books_list.append(title_text)
                 count += 1

            # Optional: Limit the number of titles extracted for this example
            # if count >= 20:
            #     break 
                 
    # 5. Store/Process
    print(f"\n--- Extracted Top {len(top_books_list)} Book Titles (Sample) ---")
    # Print the first 10 extracted titles
    for i, title in enumerate(top_books_list[:10]):
        print(f"{i+1}. {title}")
        
    # Add a small delay before finishing (good practice)
    time.sleep(1) 

except requests.exceptions.RequestException as e:
    print(f"Error during requests to {url}: {e}")
except Exception as e:
    print(f"An error occurred: {e}")

```

**Refining Extraction:**

*   The key is **inspecting the HTML** carefully using browser developer tools.
*   Look for unique IDs (`id="unique-id"`), classes (`class="item-name"`), or tag structures (`div > h2 > a`) that reliably contain your target data.
*   BeautifulSoup's `select()` method using CSS selectors is often very powerful:
    *   `soup.select('div.content')` selects all `<div>` elements with class `content`.
    *   `soup.select('#main-title')` selects the element with `id="main-title"`.
    *   `soup.select('ol > li > a')` selects `<a>` tags that are direct children of `<li>` tags, which are direct children of an `<ol>` tag.

Web scraping is a powerful but sometimes fiddly technique. Always prioritize ethical considerations and look for APIs first.

➡️ **Next Step:** [[DH Intermediate - 05 Text Analysis with NLTK - Tokenization and Stop Words]]

---

*Back to [[Welcome to the DHP]] | [[DH Intermediate - Start Here]]*