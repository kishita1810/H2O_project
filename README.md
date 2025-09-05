# H2O_project - COCO Sky Removal & Cropping Processor

This repository provides a robust pipeline to automatically remove sky regions from COCO images, crop images, and save updated COCO-format annotations ready for deep learning training. The project uses Detectron2 for panoptic segmentation and ensures a COCO-compatible dataset after preprocessing.

---

## Table of Contents
1. [Project Overview](#project-overview)
2. [Versions](#versions)
3. [Features](#features)
4. [Methodology](#methodology)
5. [Installation & Known Issues](#installation--known-issues)
6. [Usage / Implementation Steps](#usage--implementation-steps)
7. [Output Structure](#output-structure)
8. [License](#license)

---

## Project Overview

The goal is to preprocess COCO images for tasks where sky pixels are irrelevant. The pipeline:
1. Removes sky pixels using panoptic segmentation.  
2. Crops images to exclude all sky regions.  
3. Updates COCO annotations (masks and bounding boxes) for the cropped images.  
4. Provides visualization for original, sky-removed, and cropped images with annotations.

---

## Versions

**Version 1 (V1)**  
- A simple, standalone script demonstrating core sky removal and cropping logic.  
- Suitable for testing on small subsets of images.  
- Not optimized for large-scale processing.  
- Useful for understanding the processing steps and visualization.

**Version 2 (V2)**  
- Object-oriented and modular.  
- Can handle the full COCO dataset.  
- Processes images sequentially (Colab-safe) with automatic annotation updates.  
- Saves cropped images and COCO-format JSON annotations in folders mimicking COCO structure.  
- Includes visualization methods for original, sky-removed, and cropped images.  
- Supports incremental saving of partial annotations to prevent data loss during large-scale processing.  

---

## Features
- Sky removal using Detectron2 panoptic segmentation.  
- Cropping images to remove all sky pixels.  
- Updating annotations for cropped images (masks, bounding boxes).  
- Saving images and annotations in a COCO-like folder structure.  
- Side-by-side visualization of original, sky-removed, and cropped images with annotations.  
- Colab-compatible sequential processing for large datasets.  
- Incremental saving of intermediate annotation files for safety.  

---

## Methodology

1. **Panoptic Segmentation**  
   - Detectron2 predicts panoptic segmentation masks.  
   - Sky pixels are identified using COCO category ID = 40.

2. **Sky Mask Creation**  
   - Binary masks are generated where sky pixels are set to 1 (255).  

3. **Sky Removal**  
   - `cv2.bitwise_and` removes sky pixels from the original image.  

4. **Sky Cropping**  
   - Top rows containing sky are removed from the image.  
   - Edge cases where cropping would remove the entire image are handled safely.  

5. **Annotation Update**  
   - Masks and bounding boxes are recalculated to match cropped images using pycocotools.  
   - Updates are saved in COCO JSON format.

6. **Visualization**  
   - Original, sky-removed, and cropped images are visualized with updated COCO annotations using Detectron2 Visualizer.

---

## Installation & Known Issues

**Detectron2 Installation (Colab)**  
- Install from GitHub:  
```bash
pip install 'git+https://github.com/facebookresearch/detectron2.git'
```

**Common issues:**

- Directory errors: pip installs into the Python environment, not local folders.
- CUDA mismatch: Colab provides CUDA 12.x; ensure PyTorch matches.
- Multiprocessing Limitations: Colab often fails with multiprocessing due to pickling of local functions. Sequential processing is recommended in Colab.

---

## Usage / Implementation Steps

1. **Initialize processor:**

```
from detectron2.engine import DefaultPredictor
from detectron2.data import MetadataCatalog

predictor = DefaultPredictor(cfg)
metadata = MetadataCatalog.get("coco_2017_val")
processor = CocoSkyProcessor(coco, predictor, metadata)
```

2. **Process COCO images sequentially:**

```
sample_ids = coco.getImgIds()[:10]  # or use all image IDs
processor.process_dataset_sequential(sample_ids)
```

3. **Save new COCO annotations:**

```
processor.save_new_coco_json()
```

4. **Visualize processed images:**

```
processor.visualize_processed_image_with_annotations(sample_ids[0])
```

---

## Output Structure

```
coco_sky_removed/
├── images/
│   ├── cropped/       # Cropped images
│   └── no_sky/        # Images with sky removed but not cropped
└── annotations/
    ├── instances_cropped.json           # Final COCO annotations
    └── instances_cropped_partial_*.json # Partial saves during processing
```
Maintains COCO folder structure for seamless integration with deep learning pipelines.

---

## License

MIT License
