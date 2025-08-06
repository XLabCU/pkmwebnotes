---
title: "07: Geospatial Data Analysis with GeoPandas"
created: 2025-08-05T12:57:24.739Z
tags: [advanced]
---
# DH Advanced - 07: Geospatial Data Analysis with GeoPandas

Now that we have converted place names into coordinates ([[DH Advanced - 06 Geocoding - Turning Place Names into Coordinates]]), we can start performing spatial analysis. **GeoPandas** is the essential library for this in Python. It extends the datatypes used by `pandas` to allow spatial operations on geometric types, making it easy to work with geospatial vector data.

**Core Concepts:**

*   **Geometric Objects:** GeoPandas uses the `shapely` library to represent geographic features like Points, Lines, and Polygons.
*   **`GeoSeries`:** A pandas `Series` that holds geometric objects (e.g., a column of Points).
*   **`GeoDataFrame`:** A pandas `DataFrame` that includes a special `GeoSeries` column (usually named 'geometry'). This is the primary data structure. It can hold regular data (like text, numbers) alongside the spatial information.
*   **Coordinate Reference System (CRS):** Crucial for defining how the coordinates relate to locations on the Earth. GeoPandas uses `pyproj` to handle CRS information. Common CRS include:
    *   `EPSG:4326`: WGS 84 Latitude/Longitude - standard for GPS and web mapping.
    *   Various projected CRS (e.g., UTM zones) that are better for accurate distance/area calculations in specific regions (measured in meters, feet, etc.).

**Installation:**

Installing GeoPandas and its dependencies (like GDAL, Fiona, Pyproj, Shapely) can sometimes be tricky with `pip`. Using `conda` is often recommended:
`conda install geopandas`

Alternatively, try with `pip`:
`pip install geopandas matplotlib`
*(You might need to install GDAL and other libraries separately depending on your system. Consult the GeoPandas installation guide.)*

**Setup:**

Let's load the geocoded data from the previous step and create a GeoDataFrame.

```python
import pandas as pd
import geopandas as gpd
from shapely.geometry import Point # To create point geometries
import matplotlib.pyplot as plt

# --- Configuration ---
# Use the filename with geocoded results from the previous step
input_filename = "geocoded_places.csv" # Make sure this file exists from step 06

# --- Load Geocoded Data ---
try:
    df = pd.read_csv(input_filename)
    print(f"Loaded {len(df)} results from '{input_filename}'")

    # Drop rows where geocoding failed (latitude or longitude is NaN)
    original_count = len(df)
    df.dropna(subset=['latitude', 'longitude'], inplace=True)
    print(f"Kept {len(df)} rows with valid coordinates (dropped {original_count - len(df)}).")

    if df.empty:
        print("No valid coordinates found in the input file. Cannot proceed.")
        gdf_points = None # Set to None to skip subsequent steps
    else:
        # --- Create Point Geometries ---
        # Use geopandas helper function or manually create Shapely Points
        geometry = gpd.points_from_xy(df['longitude'], df['latitude'])

        # --- Create GeoDataFrame ---
        gdf_points = gpd.GeoDataFrame(df, geometry=geometry)

        # --- Set the Coordinate Reference System (CRS) ---
        # Geocoding typically returns WGS84 Latitude/Longitude (EPSG:4326)
        gdf_points.set_crs("EPSG:4326", inplace=True)

        print("\n--- GeoDataFrame Created ---")
        print(gdf_points.head())
        print(f"\nCRS Info: {gdf_points.crs}")

except FileNotFoundError:
    print(f"Error: Input file '{input_filename}' not found.")
    print("Please run the previous notebook (DH Advanced - 06) first.")
    gdf_points = None
except Exception as e:
    print(f"An error occurred: {e}")
    gdf_points = None

```

**Reading Spatial Data Files:**

GeoPandas can read various vector file formats like Shapefiles (`.shp`), GeoJSON (`.geojson`), GeoPackage (`.gpkg`), etc. It comes with some built-in datasets for convenience.

```python
# Load a built-in dataset (world boundaries)
try:
    world = gpd.read_file(gpd.datasets.get_path('naturalearth_lowres'))
    print("\n--- World Boundaries GeoDataFrame ('naturalearth_lowres') ---")
    print(world.head())
    print(f"\nWorld CRS Info: {world.crs}") # Often EPSG:4326 as well

    # Filter for a specific region if desired (e.g., North America)
    # north_america = world[world['continent'] == 'North America']

except Exception as e:
    print(f"Error loading world dataset: {e}")
    world = None

```

**Basic Plotting:**

GeoPandas integrates with `matplotlib` for quick static visualizations.

```python
if gdf_points is not None and world is not None:
    print("\n--- Plotting Geocoded Points on World Map ---")
    # Create a plot axis
    fig, ax = plt.subplots(1, 1, figsize=(15, 10)) # Define figure size

    # Plot the world map as the base layer
    world.plot(ax=ax, color='lightgray', edgecolor='black')

    # Plot our geocoded points on top
    gdf_points.plot(ax=ax, marker='o', color='red', markersize=25, label='Geocoded Places')

    # Add titles and labels (optional)
    ax.set_title('Geocoded Locations')
    ax.set_xlabel('Longitude')
    ax.set_ylabel('Latitude')
    # Optional: Turn off axis values for a cleaner map look
    # ax.set_axis_off()

    # Add a legend (if labels were provided in plot calls)
    # ax.legend() # Might be less useful for just points vs polygons

    plt.show()
else:
    print("\nSkipping plotting as GeoDataFrames are not available.")

```

**Spatial Joins:**

A powerful feature is the spatial join, which combines GeoDataFrames based on their spatial relationship (e.g., points *within* polygons).

```python
if gdf_points is not None and world is not None:
    print("\n--- Performing Spatial Join (Points within Countries) ---")

    # Ensure both GeoDataFrames have the same CRS before joining
    # Our world dataset might already be 4326, but it's good practice to check/set
    if gdf_points.crs != world.crs:
        print(f"Warning: CRS mismatch. World CRS: {world.crs}, Points CRS: {gdf_points.crs}. Attempting to align...")
        try:
            # Project world data to match points data CRS
             world = world.to_crs(gdf_points.crs)
             print(f"World data reprojected to {world.crs}")
        except Exception as e_proj:
             print(f"Error reprojecting world data: {e_proj}. Spatial join might be inaccurate.")


    # Perform the spatial join
    # 'op' specifies the spatial predicate: 'intersects', 'contains', 'within'
    # 'how='inner'' keeps only points that fall within a polygon
    # 'how='left'' keeps all points, adding polygon data where matches occur
    try:
        gdf_joined = gpd.sjoin(gdf_points, world, how='left', op='within') # 'within' is standard for points in polygons

        print("\n--- GeoDataFrame After Spatial Join ---")
        # Show original place name, coords, and joined country info
        print(gdf_joined[['PlaceName', 'latitude', 'longitude', 'name', 'continent']].head())

        # Example: Count points per country found
        country_counts = gdf_joined['name'].value_counts()
        print("\n--- Counts per Country (from Spatial Join) ---")
        print(country_counts)

    except Exception as e_sjoin:
        print(f"Error during spatial join: {e_sjoin}")
        # Potentially due to CRS issues or invalid geometries

else:
    print("\nSkipping spatial join as GeoDataFrames are not available.")

```

**Other Spatial Operations (Brief Mention):**

GeoPandas allows for many other operations:

*   **Buffering:** Creating areas around points/lines (`gdf.buffer(distance)`).
*   **Calculating Distances:** Finding distances between geometries (`gdf1.distance(gdf2)`). *Requires appropriate projected CRS for meaningful units.*
*   **Set Operations:** Finding intersections, unions, differences between geometries (`gdf.intersection(other_gdf)`).
*   **Attribute Joins:** Merging based on common non-spatial columns (like standard pandas `merge`).

**Conclusion:**

GeoPandas provides a powerful and intuitive framework for working with geospatial vector data in Python, integrating seamlessly with the pandas ecosystem. It enables you to read spatial files, manage CRS, perform spatial queries and joins, and create basic maps, laying the groundwork for more sophisticated geospatial analysis and visualization in DH projects.

➡️ **Next Step:** [[DH Advanced - 08 Interactive Mapping]] *(To create more engaging and explorable maps)*

---

*Back to [[Welcome to the DHP]] | [[DH Advanced - Start Here]]*