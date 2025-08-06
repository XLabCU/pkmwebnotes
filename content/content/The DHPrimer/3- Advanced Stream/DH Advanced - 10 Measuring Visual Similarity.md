---
title: "10: Measuring Visual Similarity"
created: 2025-08-05T12:57:24.739Z
tags: [advanced]
---
# DH Advanced - 10: Measuring Visual Similarity

Once we can load and represent images as data ([[DH Advanced - 09 Introduction to Image Analysis Basics]]), a common task is to compare them and quantify their **visual similarity**. This is useful for:

*   Finding duplicate or near-duplicate images.
*   Grouping images based on visual style (e.g., color palette, texture).
*   Content-based image retrieval (finding images similar to a query image).
*   Analyzing stylistic trends across a collection.

**Challenge:** What does "similar" mean visually? Similarity can relate to color, texture, shape, object composition, artistic style, etc. Different methods capture different aspects of similarity.

**Approaches:**

1.  **Pixel-wise Comparison:** Directly compare pixel values (e.g., calculate Mean Squared Error). Very sensitive to small shifts, rotations, or lighting changes; generally not robust for perceptual similarity.
2.  **Feature-Based Comparison:**
    *   Extract meaningful **features** (numerical representations) from each image that capture specific visual characteristics.
    *   Calculate the **distance** or **similarity** between these feature vectors. Images with closer feature vectors are considered more similar.

**Libraries:**

*   **scikit-image:** Provides algorithms for image processing and feature extraction. (`pip install scikit-image`)
*   **NumPy:** For array manipulation and distance calculations.
*   **Pillow, Matplotlib:** For image loading and display.

**Method 1: Color Histogram Similarity**

A simple but often effective feature is the **color histogram**, which represents the distribution of colors within an image.

*   **Process:**
    1.  Calculate the color histogram for each image (often separately for R, G, B channels).
    2.  Flatten histograms into 1D feature vectors.
    3.  Compare these vectors using a distance metric (e.g., Euclidean distance, Chi-Squared distance, histogram intersection).

**Example:**

```python
from PIL import Image
import matplotlib.pyplot as plt
import numpy as np
import os
from skimage import io # scikit-image provides convenient image loading
from skimage.metrics import structural_similarity as ssim # For later example
from scipy.spatial.distance import euclidean # For histogram distance

# --- Configuration ---
# Need at least two images to compare. Make sure they exist.
image_file1 = "image1.jpg" # Replace with your first image
image_file2 = "image2.jpg" # Replace with your second image (ideally visually related/unrelated to image1)
image_file3 = "image3.jpg" # Replace with a third image

# --- Helper function to calculate and flatten RGB histogram ---
def calculate_rgb_histogram(image_array, bins=32):
    """Calculates separate R, G, B histograms and concatenates them."""
    # Ensure image is 3D (RGB)
    if image_array.ndim != 3 or image_array.shape[2] != 3:
        # Attempt to convert if RGBA or Grayscale (basic handling)
        if image_array.ndim == 3 and image_array.shape[2] == 4: # RGBA
             image_array = image_array[:, :, :3] # Drop alpha
        elif image_array.ndim == 2: # Grayscale
             # Convert grayscale to pseudo-RGB by stacking
             image_array = np.stack((image_array,)*3, axis=-1)
        else:
             raise ValueError("Input must be an RGB, RGBA, or Grayscale image array.")

    hist_r, _ = np.histogram(image_array[:, :, 0].ravel(), bins=bins, range=(0, 256))
    hist_g, _ = np.histogram(image_array[:, :, 1].ravel(), bins=bins, range=(0, 256))
    hist_b, _ = np.histogram(image_array[:, :, 2].ravel(), bins=bins, range=(0, 256))

    # Normalize histograms (optional but often good practice)
    hist_r = hist_r / hist_r.sum() if hist_r.sum() > 0 else hist_r
    hist_g = hist_g / hist_g.sum() if hist_g.sum() > 0 else hist_g
    hist_b = hist_b / hist_b.sum() if hist_b.sum() > 0 else hist_b

    # Concatenate into a single feature vector
    return np.concatenate((hist_r, hist_g, hist_b))

# --- Load Images and Calculate Histograms ---
image_paths = [image_file1, image_file2, image_file3]
histograms = {}
images_loaded = {}
valid_paths = []

print("--- Calculating Color Histograms ---")
for img_path in image_paths:
    if os.path.exists(img_path):
        try:
            print(f"Processing: {img_path}")
            # Load using scikit-image (handles different types well)
            img = io.imread(img_path)
            images_loaded[img_path] = img # Store loaded image for later use (SSIM)
            histograms[img_path] = calculate_rgb_histogram(img)
            valid_paths.append(img_path)
            print(f"  -> Histogram calculated (vector length: {len(histograms[img_path])})")
        except Exception as e:
            print(f"  -> Error processing {img_path}: {e}")
    else:
        print(f"Skipping non-existent file: {img_path}")

# --- Compare Histograms using Euclidean Distance ---
# Compare image 1 to image 2 and image 1 to image 3
if len(valid_paths) >= 3 and valid_paths[0] in histograms and valid_paths[1] in histograms and valid_paths[2] in histograms:
    hist1 = histograms[valid_paths[0]]
    hist2 = histograms[valid_paths[1]]
    hist3 = histograms[valid_paths[2]]

    dist_1_2 = euclidean(hist1, hist2)
    dist_1_3 = euclidean(hist1, hist3)

    print("\n--- Histogram Similarity (Euclidean Distance) ---")
    # Lower distance means more similar histograms (color distributions)
    print(f"Distance between '{valid_paths[0]}' and '{valid_paths[1]}': {dist_1_2:.4f}")
    print(f"Distance between '{valid_paths[0]}' and '{valid_paths[2]}': {dist_1_3:.4f}")

    if dist_1_2 < dist_1_3:
        print(f"-> Based on color histogram, '{valid_paths[1]}' is more similar to '{valid_paths[0]}' than '{valid_paths[2]}'.")
    elif dist_1_3 < dist_1_2:
         print(f"-> Based on color histogram, '{valid_paths[2]}' is more similar to '{valid_paths[0]}' than '{valid_paths[1]}'.")
    else:
         print(f"-> Images '{valid_paths[1]}' and '{valid_paths[2]}' have similar histogram distance to '{valid_paths[0]}'.")

else:
    print("\nCould not perform histogram comparison. Need at least 3 valid images processed.")

```

**Method 2: Structural Similarity Index (SSIM)**

SSIM is designed to be a perceptual metric, meaning it aims to quantify similarity based on how humans perceive structure, luminance, and contrast, rather than just color distribution. It returns a score between -1 and 1, where 1 indicates perfect similarity.

*   **Process:**
    1.  Compare two images directly using `skimage.metrics.structural_similarity`.
    2.  Images should generally be grayscale and the same size for reliable comparison.

**Example:**

```python
from skimage.transform import resize # To resize images to the same dimensions
from skimage.color import rgb2gray # To convert to grayscale

if len(valid_paths) >= 2 and valid_paths[0] in images_loaded and valid_paths[1] in images_loaded:
    print("\n--- Structural Similarity Index (SSIM) ---")
    # --- Prepare Images for SSIM ---
    # Load images if not already loaded (should be in images_loaded)
    img1 = images_loaded[valid_paths[0]]
    img2 = images_loaded[valid_paths[1]]
    img3 = images_loaded.get(valid_paths[2]) # Use .get() for the third image

    # Convert to grayscale
    img1_gray = rgb2gray(img1) if img1.ndim == 3 else img1
    img2_gray = rgb2gray(img2) if img2.ndim == 3 else img2
    if img3 is not None:
      img3_gray = rgb2gray(img3) if img3.ndim == 3 else img3

    # Resize images to a common size (e.g., size of the first image)
    # Anti-aliasing helps prevent artifacts during resizing
    target_shape = img1_gray.shape
    img2_gray_resized = resize(img2_gray, target_shape, anti_aliasing=True)
    if img3 is not None:
       img3_gray_resized = resize(img3_gray, target_shape, anti_aliasing=True)

    # --- Calculate SSIM ---
    # data_range is the range of pixel values (1.0 for float images from rgb2gray)
    ssim_1_2, diff_1_2 = ssim(img1_gray, img2_gray_resized, data_range=1.0, full=True) # full=True returns diff image
    print(f"SSIM between '{valid_paths[0]}' and '{valid_paths[1]}': {ssim_1_2:.4f}")

    if img3 is not None:
       ssim_1_3, diff_1_3 = ssim(img1_gray, img3_gray_resized, data_range=1.0, full=True)
       print(f"SSIM between '{valid_paths[0]}' and '{valid_paths[2]}': {ssim_1_3:.4f}")

       if ssim_1_2 > ssim_1_3:
          print(f"-> Based on SSIM, '{valid_paths[1]}' is more structurally similar to '{valid_paths[0]}' than '{valid_paths[2]}'.")
       elif ssim_1_3 > ssim_1_2:
          print(f"-> Based on SSIM, '{valid_paths[2]}' is more structurally similar to '{valid_paths[0]}' than '{valid_paths[1]}'.")
       else:
           print(f"-> Images '{valid_paths[1]}' and '{valid_paths[2]}' have similar SSIM scores relative to '{valid_paths[0]}'.")

       # --- Optional: Display Difference Images ---
       fig, axs = plt.subplots(1, 3, figsize=(15, 5))
       axs[0].imshow(img1_gray, cmap='gray')
       axs[0].set_title(f'Original: {valid_paths[0]}')
       axs[0].axis('off')
       axs[1].imshow(diff_1_2, cmap='gray')
       axs[1].set_title(f'Difference with {valid_paths[1]}\nSSIM: {ssim_1_2:.3f}')
       axs[1].axis('off')
       axs[2].imshow(diff_1_3, cmap='gray')
       axs[2].set_title(f'Difference with {valid_paths[2]}\nSSIM: {ssim_1_3:.3f}')
       axs[2].axis('off')
       plt.tight_layout()
       plt.show()

else:
    print("\nCould not perform SSIM comparison. Need at least 2 (or 3 for full comparison) valid images processed.")


```

**Advanced Methods (Brief Mention):**

*   **Texture Features:** Analyze patterns and textures using methods like Local Binary Patterns (LBP) or Gabor filters.
*   **Keypoint Descriptors:** Identify salient points (keypoints) using algorithms like SIFT, SURF, or ORB and describe the region around them. Compare images based on matching descriptors.
*   **Deep Learning Embeddings:** Use pre-trained Convolutional Neural Networks (CNNs) like VGG, ResNet, or CLIP to extract high-level feature vectors (embeddings) from images. Images with similar content or style tend to have closer embeddings in the feature space (often compared using Cosine Similarity). This is currently state-of-the-art for many semantic similarity tasks but requires libraries like `tensorflow` or `pytorch`.

**Choosing a Method:**

*   **Color Histograms:** Good for comparing overall color palettes, fast. Insensitive to spatial arrangement.
*   **SSIM:** Better for perceived structural similarity, good for finding near-duplicates or assessing compression quality. Requires images to be aligned and same size.
*   **Advanced/Deep Learning:** Best for semantic similarity (identifying similar objects or scenes regardless of viewpoint/lighting) or complex stylistic similarity, but require more setup and computational resources.

Measuring visual similarity is complex, but feature-based approaches provide quantitative methods to compare images based on specific characteristics relevant to DH research questions.

➡️ **Next Step:** [[DH Advanced - 11 Introduction to Audio Analysis Basics]]

---

*Back to [[Welcome to the DHP]] | [[DH Advanced - Start Here]]*