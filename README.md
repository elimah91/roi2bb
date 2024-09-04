# ROI2BB
### ROI to YOLO Bounding Box Converter

This repository provides a Python class, ROI2BB, for converting Regions of Interest (ROI) stored in 3D Slicer JSON format into bounding box coordinates in YOLO format. These two coordinate systems have 3 main differences that should be addressed while converting:

1. The Slicer output follows the "Patient coordinate system", in which its origin is located at an anatomical landmark not necessarily in the image boundaries, while the YOLO-compatible input format is based on the "Image coordinate system", in which its origin is located at the upper-left corner of the image. Also, the axis directions are not the same in different coordinate systems, changing the point coordinate values.
    
2. The Slicer format dimensions are the actual ROI dimensions, while the Yolo format dimensions are a ratio of ROI dimension to image dimensions. 

3. The Slicer output is in JSON format with the ROI 'center' coordinates (x,y,z) and ROI dimensions 'size' (x_length, y_length,z_length) reported under 'markups' tag, while YOLO-compatible input is a text file with each line presenting one ROI containing: 

    "class center_z center_x center_y z_length x_length y_length"

    The Slicer gives one JSON file for each ROI, while a single YOLO text file contains multiple ROIs for multiple classes, each class defined by a unique index and each ROI reported in a separate line.
     
For each image, ROI2BB receives the path to the NIfTI image file, the path to the folder containing JSON files (one for each ROI) and the desired path to save the YOLO-compatible text file.

The expected data structure is:

```
project_directory/
├── images/
│   ├── Patient_001.nii or .nii.gz
│   └── Patient_002.nii
├── labels/
│   ├── Patient_001/
│   │   ├── left_atrium.json
│   │   ├── lymph_node_1.json
│   │   ├── lymph_node_2.json
│   │   ├── lymph_node_3.json
│   │   └── trachea.json
│   ├── Patient_002/
│   │   ├── left_atrium.json
│   │   ├── lymph_node.json
│   │   └── trachea.json
└── output/
    └── yolo_format.txt
```    
### Table of Contents

- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
- [Directory Structure](#directory-structure)
- [Customization](#customization)
- [License](#license)

### Requirements

- Python 3.x
- nibabel (for handling NIfTI files)
- glob
- json

### Example Usage

Here’s an example of how to use the ROI2BB class to convert ROIs to YOLO format:

```bash
from roi2bb.converter import ROI2BB

converter = ROI2BB("path_to_nifti_file.nii", "path_to_json_folder", "output_yolo_format.txt")

converter.run()
```
### Class Index Mapping

The method get_class_index maps ROI names to YOLO class indices. You can customize the class labels by editing the dictionary inside this method:

```bash
def get_class_index(self, class_label: str) -> int:
    class_mapping = {
        "left_atrium": 0,
        "trachea": 1,
        "lymph_node": 2,
        # Add more class labels and indices here
    }
    return class_mapping.get(class_label, -1)
```
### Example Output
```bash
0 0.523 0.312 0.532 0.128 0.276 0.345  # left_atrium
2 0.734 0.512 0.723 0.132 0.254 0.367  # lymph_node_1
2 0.834 0.612 0.823 0.152 0.274 0.447  # lymph_node_2
1 0.634 0.412 0.623 0.112 0.234 0.287  # trachea
```bash
