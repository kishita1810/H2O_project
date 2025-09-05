# H2O_project

# COCO Sky Removal & Cropping Processor

This repository contains code to automatically **remove the sky from COCO images**, crop the images, and save updated **annotations** for use in deep learning tasks. The project uses **Detectron2** for panoptic segmentation and ensures the dataset is ready for training after preprocessing.

---

## Versions of the Code

This repository contains **two versions** of the implementation:

1. **Version 1 (V1)**
   - A simple, standalone script that demonstrates the core logic of sky removal and cropping.
   - Designed for testing and verifying the algorithm on a small subset of images.
   - Not optimized for processing the full COCO dataset.
   - Useful for understanding the processing steps and visualization.

2. **Version 2 (V2)**
   - An object-oriented, modular implementation that can handle the **entire COCO dataset**.
   - Processes images sequentially (Colab-safe) and updates annotations automatically.
   - Saves cropped images and corresponding COCO-format JSON annotations in separate folders, mimicking the original COCO structure.
   - Includes visualization methods for original, sky-removed, and cropped images with annotations.
   - Incremental saving of partial annotations prevents data loss during large-scale processing.
   - Designed for full dataset preparation for deep learning training.

---

## Features

- Sky removal using Detectron2 panoptic segmentation
- Cropping images to remove all sky pixels
- Updating annotations for cropped images
- Saving images and annotations in separate folders (similar to COCO structure)
- Side-by-side visualization of original, sky-removed, and cropped images with annotations
- Colab-compatible, sequential processing for large datasets
- Incremental saving of intermediate annotation files for safety

---

## Installation & Known Issues

1. **Detectron2 Installation on Colab**
   - Detectron2 is not included by default and must be installed from the official GitHub repository.
   - Common issues:
     - **Directory errors:** Attempting to `cd` into a `detectron2` folder will fail because pip installs it in the Python environment, not as a local folder.
     - **CUDA version mismatch:** Colab usually provides CUDA 12.x, while some Detectron2 versions require specific CUDA versions. Installing the latest GitHub version usually resolves this.
     - **Dependencies:** PyTorch must be installed and compatible with the CUDA version in Colab.

2. **Multiprocessing Limitations**
   - In typical Python environments, multiprocessing can speed up processing.
   - In Colab, **multiprocessing often fails** due to pickling limitations with local functions or class methods.
   - Using multiprocessing can cause errors like:
     ```
     AttributeError: Can't get local object 'CocoSkyProcessor.process_dataset_parallel.<locals>._process'
     ```
   - **Solution:** Process images sequentially in Colab, which is slower but stable for thousands of images.

---

## Notes on Processing

1. **Sequential Processing**
   - Images are processed one by one to ensure compatibility with Colab.
   - Intermediate annotation files are saved every `N` images (configurable) to prevent data loss if the session crashes.

2. **Sky Removal & Cropping**
   - Sky pixels are removed using Detectron2 panoptic segmentation.
   - Cropping removes all sky pixels from the top of the image, even if some other objects are partially removed.
   - Annotations are updated to match cropped images, making the dataset ready for training directly.

3. **Improvements for Local or HPC Environments**
   - Multiprocessing can be enabled for faster processing by moving the processing function to a **top-level function** outside the class.
   - Additional improvements:
     - Chunking datasets and saving partial JSON files to allow resuming after interruptions.
     - GPU acceleration for image processing (OpenCV + CUDA).

---

## Output Structure

- **images/cropped/** → contains processed and cropped images  
- **annotations/** → contains updated COCO annotation JSON files, including partial saves and final dataset JSON  
- Maintains a structure similar to the original COCO dataset for ease of use in deep learning workflows.

---

## License

MIT License

---
