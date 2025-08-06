---
title: "09: Introduction to Image Analysis Basics"
created: 2025-08-05T12:57:24.739Z
tags: [advanced]
---
# DH Advanced - 09: Introduction to Image Analysis Basics

Digital images are a rich source of information in many humanities disciplines – art history, manuscript studies, archaeology, visual culture studies, etc. Computational image analysis allows us to process, manipulate, and extract quantitative information from images at scale.

**Core Concepts:**

*   **Digital Image:** A representation of a visual scene composed of discrete picture elements called **pixels**.
*   **Pixels:** Each pixel has a specific location (x, y coordinates) and a value representing its color or intensity.
*   **Resolution:** The dimensions of the image in pixels (width x height).
*   **Color Models:** Ways of representing color numerically:
    *   **Grayscale:** Each pixel has a single value representing intensity (e.g., 0=black, 255=white).
    *   **RGB (Red, Green, Blue):** Each pixel has three values, representing the intensity of red, green, and blue components. This is standard for color displays and many image formats.
    *   **RGBA:** RGB plus an Alpha channel representing transparency/opacity.
    *   **Other models:** HSV (Hue, Saturation, Value), CMYK (Cyan, Magenta, Yellow, Key/Black - used in printing).
*   **Image as Data:** In Python, images are typically loaded and manipulated as **NumPy arrays**.
    *   A grayscale image is often a 2D array (height x width).
    *   An RGB color image is often a 3D array (height x width x 3 channels).

**Libraries:**

*   **Pillow (PIL Fork):** The fundamental library for opening, manipulating, and saving many different image file formats in Python. (`pip install Pillow`)
*   **NumPy:** For numerical operations on the image arrays. (`pip install numpy`)
*   **Matplotlib:** For displaying images within Python environments. (`pip install matplotlib`)

**Setup: Loading and Displaying an Image:**

First, you need an image file. You can use one of your own or download a sample (e.g., search for open-access images from museum collections like The Met, Rijksmuseum, or use standard test images). Save it in the same folder as your notebook. Let's assume you have `sample_image.jpg`.

```python
from PIL import Image
import matplotlib.pyplot as plt
import numpy as np
import os # To check if file exists

# --- Configuration ---
image_filename = "sample_image.jpg" # Replace with your image file name

# --- Check if image exists ---
if not os.path.exists(image_filename):
    print(f"Error: Image file '{image_filename}' not found in the current directory.")
    # You might want to add code here to download a default image if needed
    # For example:
    # import requests
    # try:
    #     print("Downloading a sample image (Van Gogh)...")
    #     url = "https://collections.metmuseum.org/public/collection/v1/images/436535/mobile-large" # Example URL (check terms)
    #     response = requests.get(url, stream=True)
    #     response.raise_for_status()
    #     with open(image_filename, 'wb') as f:
    #         for chunk in response.iter_content(chunk_size=8192):
    #             f.write(chunk)
    #     print(f"Sample image saved as '{image_filename}'")
    # except Exception as download_err:
    #     print(f"Failed to download sample image: {download_err}")
    #     image_filename = None # Prevent further errors if download fails
else:
    print(f"Using image file: '{image_filename}'")


# --- Load the Image using Pillow ---
if image_filename and os.path.exists(image_filename):
    try:
        img_pil = Image.open(image_filename)

        # --- Basic Image Information ---
        print("\n--- Image Information (Pillow) ---")
        print(f"Format: {img_pil.format}")
        print(f"Size (Width x Height): {img_pil.size}")
        print(f"Mode (Color channels): {img_pil.mode}") # e.g., 'RGB', 'L' (grayscale), 'RGBA'

        # --- Display the Image using Matplotlib ---
        plt.figure(figsize=(8, 6)) # Control display size
        plt.imshow(img_pil)
        plt.title(f"Original Image: {image_filename}")
        plt.axis('off') # Hide axes ticks/labels
        plt.show()

        # --- Convert to NumPy array for numerical access ---
        img_array = np.array(img_pil)
        print("\n--- Image as NumPy Array ---")
        print(f"Array data type: {img_array.dtype}") # e.g., uint8 (0-255)
        print(f"Array shape (Height, Width, Channels): {img_array.shape}")

        # Accessing pixel data (Example: pixel at row 10, column 20)
        if img_array.shape[0] > 10 and img_array.shape[1] > 20:
             pixel_value = img_array[10, 20]
             print(f"Pixel value at (10, 20): {pixel_value}") # Will be [R G B] for color images
        else:
             print("Image too small to access pixel (10, 20).")


    except FileNotFoundError:
        # This check is somewhat redundant due to the os.path.exists check above,
        # but good practice in case the filename variable was incorrect.
        print(f"Error: Image file '{image_filename}' could not be opened (double-check path).")
    except Exception as e:
        print(f"An error occurred processing the image: {e}")
else:
    print("\nCannot proceed without a valid image file.")


```

**Basic Manipulations with Pillow:**

Pillow provides simple methods for common transformations. Remember these often return a *new* Image object.

```python
# Ensure img_pil exists from the previous cell
if 'img_pil' in locals() and img_pil is not None:
    # --- Convert to Grayscale ---
    img_gray = img_pil.convert('L')

    # --- Resize ---
    # Provide a tuple (width, height)
    new_size = (200, 150) # Example size
    img_resized = img_pil.resize(new_size)

    # --- Crop ---
    # Provide a tuple (left, upper, right, lower) pixel coordinates
    # Crop a 100x100 box from the top-left corner
    box = (0, 0, 100, 100)
    img_cropped = img_pil.crop(box)

    # --- Rotate ---
    img_rotated = img_pil.rotate(45) # Rotates counter-clockwise by degrees

    # --- Display Manipulated Images ---
    fig, axs = plt.subplots(2, 2, figsize=(10, 8)) # Create 2x2 grid of plots
    axs[0, 0].imshow(img_gray, cmap='gray') # Use grayscale colormap for 'L' mode
    axs[0, 0].set_title('Grayscale')
    axs[0, 0].axis('off')

    axs[0, 1].imshow(img_resized)
    axs[0, 1].set_title(f'Resized ({new_size[0]}x{new_size[1]})')
    axs[0, 1].axis('off')

    axs[1, 0].imshow(img_cropped)
    axs[1, 0].set_title('Cropped (100x100)')
    axs[1, 0].axis('off')

    axs[1, 1].imshow(img_rotated)
    axs[1, 1].set_title('Rotated 45°')
    axs[1, 1].axis('off')

    plt.tight_layout() # Adjust spacing
    plt.show()

    # --- Saving an Image ---
    try:
        output_filename = "processed_image.png" # Use PNG to avoid JPEG compression artifacts
        img_gray.save(output_filename)
        print(f"\nSaved grayscale image as '{output_filename}'")
    except Exception as e_save:
        print(f"\nError saving image: {e_save}")

else:
    print("\nSkipping image manipulations because 'img_pil' is not defined.")

```

**Key Takeaways:**

*   Digital images can be loaded and treated as data structures (NumPy arrays) in Python.
*   Libraries like Pillow allow easy loading, saving, and basic manipulations (resizing, cropping, color conversion).
*   Understanding image dimensions, color modes, and pixel access is fundamental for quantitative image analysis.

This forms the basis for more advanced techniques like feature extraction, object detection, and visual similarity measurement.

➡️ **Next Step:** [[DH Advanced - 10 Measuring Visual Similarity]]

---

*Back to [[Welcome to the DHP]] | [[DH Advanced - Start Here]]*