---
title: "06: Geocoding - Turning Place Names into Coordinates"
created: 2025-08-05T12:57:24.739Z
tags: [advanced]
---
# DH Advanced - 06: Geocoding - Turning Place Names into Coordinates

Much humanities data contains references to places – cities, countries, historical regions, specific addresses, landmarks. To perform spatial analysis or create maps, we need to convert these textual place names into geographic coordinates (typically latitude and longitude). This process is called **geocoding**.

**Why Geocode in DH?**

*   **Mapping:** Plot locations of events, object origins, author residences, etc., on maps.
*   **Spatial Analysis:** Calculate distances, analyze spatial distributions, identify clusters, correlate data with geographic features.
*   **Linking Datasets:** Join datasets based on geographic proximity.

**Challenges:**

*   **Ambiguity:** Place names aren't always unique (e.g., "Springfield", "Paris"). Context might be needed.
*   **Historical Names:** Place names and boundaries change over time. Geocoders might struggle with historical variants.
*   **Data Quality:** Input place names might be misspelled, incomplete, or vague ("near London").
*   **Service Limitations:** Geocoding services rely on databases that might be incomplete or have errors.

**Tools: The `geopy` Library**

`geopy` is a Python library that provides a convenient interface to several popular geocoding web services. It abstracts away the specific API details for different providers.

**Installation:**
`pip install geopy`

**Choosing a Geocoding Service:**

`geopy` supports various services, each with its own terms, potential costs, and rate limits:

*   **Nominatim (OpenStreetMap):**
    *   **Pros:** Free, uses open data from OpenStreetMap.
    *   **Cons:** **Strict usage policy** – requires fair use, **max 1 request per second**, no heavy bulk geocoding without explicit permission, requires a unique user agent string. Results can sometimes be less precise than commercial options for specific addresses. **Excellent for learning and smaller projects.**
*   **Google Maps Platform:**
    *   **Pros:** High accuracy, extensive database.
    *   **Cons:** Requires API key, has usage limits within a free tier, becomes paid beyond that. Terms of service may restrict storing results long-term.
*   **ArcGIS, OpenCage, Mapbox, etc.:** Various commercial and open options with different pricing models and features.

**For educational purposes and many DH projects, Nominatim is a good starting point, *provided you strictly adhere to its usage policy*.**

**Basic Geocoding with Nominatim:**

```python
from geopy.geocoders import Nominatim
from geopy.exc import GeocoderTimedOut, GeocoderServiceError
import time

# --- IMPORTANT: Define a unique User-Agent ---
# Replace 'YourAppNameOrEmail' with something identifiable.
# This is REQUIRED by Nominatim's Usage Policy.
geolocator = Nominatim(user_agent="DH_Primer_Geocoding_Example_YourAppNameOrEmail")

# --- Geocode a single location ---
location_name = "The British Museum, London"
print(f"Geocoding: '{location_name}'")

try:
    # Perform the geocoding lookup
    location = geolocator.geocode(location_name, timeout=10) # Add timeout

    if location:
        print(f"  -> Full Address: {location.address}")
        print(f"  -> Latitude: {location.latitude:.5f}") # Format for readability
        print(f"  -> Longitude: {location.longitude:.5f}")
        # Raw data contains more details
        # print(f"  -> Raw Data: {location.raw}")
    else:
        print("  -> Location not found.")

except GeocoderTimedOut:
    print("  -> Error: Geocoder service timed out.")
except GeocoderServiceError as e:
    print(f"  -> Error: Geocoder service error: {e}")
except Exception as e:
    print(f"  -> An unexpected error occurred: {e}")


# --- Example of a place not found ---
location_name_nf = "A made up place name xyz 123"
print(f"\nGeocoding: '{location_name_nf}'")
try:
    location_nf = geolocator.geocode(location_name_nf, timeout=10)
    if location_nf is None:
        print("  -> Location not found (as expected).")
except Exception as e:
     print(f"  -> An error occurred: {e}")

```

**Geocoding Multiple Locations (Handling Rate Limits):**

When geocoding a list of places (e.g., from a DataFrame column), you **must** introduce delays between requests to comply with the service's terms (especially Nominatim's 1 request/second limit).

**DH Application Example:** Geocoding place names extracted perhaps via NER or from metadata.

```python
import pandas as pd

# --- Sample Data (e.g., locations associated with Met objects) ---
data = {
    'ObjectID': [101, 102, 103, 104, 105, 106],
    'PlaceName': [
        "Paris, France",
        "Rome, Italy",
        "Athens, Attica, Greece", # More specific
        "London", # Ambiguous - might default to UK capital
        "Unknown Location", # Will likely fail
        "Florence, Tuscany" # Needs country context, but might work
    ]
}
df = pd.DataFrame(data)
print("--- Input DataFrame ---")
print(df)

# --- Geocoding Function with Error Handling & Rate Limiting ---
def geocode_place(place_name, geolocator, sleep_seconds=1.1):
    """Geocodes a place name, handles errors, and pauses."""
    if not isinstance(place_name, str) or not place_name.strip():
        return None # Return None for empty or non-string input

    try:
        # Pause *before* making the request (Nominatim policy)
        print(f"  Pausing for {sleep_seconds}s before geocoding '{place_name}'...")
        time.sleep(sleep_seconds)

        location = geolocator.geocode(place_name, timeout=10)
        if location:
            print(f"    -> Found: {location.address} ({location.latitude:.4f}, {location.longitude:.4f})")
            return location # Return the full location object
        else:
            print(f"    -> Not found: '{place_name}'")
            return None
    except GeocoderTimedOut:
        print(f"    -> Timeout error for: '{place_name}'")
        return None
    except GeocoderServiceError as e:
        print(f"    -> Service error for '{place_name}': {e}")
        return None
    except Exception as e:
        print(f"    -> Unexpected error for '{place_name}': {e}")
        return None

# --- Apply Geocoding to DataFrame ---
# Initialize lists to store results
locations = []
latitudes = []
longitudes = []
addresses = []

print("\n--- Starting Geocoding Process ---")
# Re-initialize geolocator (ensure user_agent is set)
geolocator = Nominatim(user_agent="DH_Primer_Geocoding_Example_YourAppNameOrEmail")

# Iterate through the 'PlaceName' column
for place in df['PlaceName']:
    location_obj = geocode_place(place, geolocator)

    # Store results (or None if failed)
    locations.append(location_obj)
    latitudes.append(location_obj.latitude if location_obj else None)
    longitudes.append(location_obj.longitude if location_obj else None)
    addresses.append(location_obj.address if location_obj else None)

# Add results as new columns to the DataFrame
df['geopy_location'] = locations # Stores the full location object (optional)
df['latitude'] = latitudes
df['longitude'] = longitudes
df['geocoded_address'] = addresses

print("\n--- DataFrame with Geocoding Results ---")
# Display relevant columns
print(df[['PlaceName', 'latitude', 'longitude', 'geocoded_address']])

```

**Limitations & Considerations:**

*   **Accuracy:** Geocoding accuracy varies by service and the specificity of the input place name. Verify results, especially for critical applications.
*   **Historical Geocoding:** Standard geocoders primarily use modern databases. Geocoding historical place names often requires specialized gazetteers (like the World Historical Gazetteer) or manual lookup and cross-referencing.
*   **Rate Limits & Costs:** Always respect usage policies. For large datasets, free services might be too slow or restrictive, necessitating paid options or alternative strategies.
*   **Terms of Service:** Be aware of how different services allow you to store and reuse the coordinates you obtain.

Geocoding is the essential first step for bringing the spatial dimension of humanities data onto the map, enabling powerful visualizations and analyses.

➡️ **Next Step:** [[DH Advanced - 07 Geospatial Data Analysis with GeoPandas]] *(To work with the generated coordinates and perform spatial operations)*

---

*Back to [[Welcome to the DHP]] | [[DH Advanced - Start Here]]*