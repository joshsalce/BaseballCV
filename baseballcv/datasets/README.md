# Datasets

This directory contains the dataset class implementations for BaseballCV. Each dataset class provides a standardized interface for loading and processing different types of datasets for computer vision and VLM model training.

## Available Dataset Formats

### 1. CocoDetectionDataset
A PyTorch dataset class that supports both hierarchical and flat COCO format structures.

#### Using the Dataset

```python
from baseballcv.datasets import CocoDetectionDataset
from transformers import DetrImageProcessor

# Initialize processor
processor = DetrImageProcessor()

# Create dataset
dataset = CocoDetectionDataset(dataset_dir="baseball_dataset", split="train", processor=processor)
```

#### Features
- Supports both hierarchical and flat COCO structures
- Automatic structure detection
- Integrated image preprocessing
- Compatible with PyTorch DataLoader
- Efficient memory management

### 2. JsonlDetectionDataset
A dataset class for handling JSONL format annotations with image paths.

#### Using the Dataset

```python
from baseballcv.datasets import JSONLDetectionDataset

# Create dataset
dataset = JSONLDetectionDataset(jsonl_file_path="annotations.jsonl", image_directory_path="images/", augment=True)
```

## Dataset Translator
The `DatasetTranslator` is a wrapper for roboflow's `DetectionDataset` class which extracts the images and annotations from your input directories and converts it to the desired dataset class. To reduce errors with running this function, we are making assumptions on the file directories you are using. If you're directory is not in line with what we expect, it can lead to errors or undesirable results. Please take a look at the expected structure below to make sure your input directories are supported by our translator. 

Example:
```python
from baseballcv.datasets import DatasetTranslator

dt = DatasetTranslator('coco', 'yolo', root_dir='dataset_dir/', conversion_dir='new_conversions/')
dt.convert()
dt.fmt_instance # If you want to check the formatted instance, should be COOCFmt
```

Expected input for YOLO:
```
Root/
| *.yaml
├── train/
|   ├── images/
|   ├── labels/
├── test/
|   ├── images/
|   ├── labels/
├── valid/ (optional)
|   ├── images/
|   ├── labels/
```

Expected input for COCO:
```
Root/
├── train/
|   ├── *.json
|   ├── images/
|   ├── labels/
├── test/
|   ├── *.json
|   ├── images/
|   ├── labels/
├── valid/ (optional)
|   ├── *.json
|   ├── images/
|   ├── labels/
```

Expected input for Pascal Voc (.xml files + images should be in these folders):
```
Root/
├── train/
├── test/
├── valid/ (optional)
```

Expected input for JSONL:
```
Root/
├── dataset/
|   ├── _annotations.train.jsonl
|   ├── _annotations.test.jsonl
|   ├── _annotations.valid.jsonl (optional)
|   ├── images.jpg (rest of images)
```

#### Features
- JSONL format support
- Optional data augmentation
- Efficient memory usage
- Streaming capability for large datasets
- Flexible annotation format

## Adding New Dataset Formats

When adding new dataset classes, follow these guidelines:

1. Create a new Python file in the formats directory
2. Implement the standard interface:
   ```python
   class NewDataset:
       def __init__(self, **kwargs):
           # Initialize dataset parameters
           pass
           
       def __len__(self) -> int:
           # Return dataset length
           pass
           
       def __getitem__(self, idx: int) -> Dict:
           # Return single item
           pass
   ```

3. Add dataset class to `__init__.py`
4. Update this README with dataset documentation

## Common Requirements

All dataset classes should:
- Include proper error handling
- Support efficient data loading
- Include docstrings and type hints
- Handle missing files gracefully
- Support data augmentation (when applicable)

## Future Formats
Future dataset classes will be added for:
- YOLO format
- Segmentation masks
- Keypoint annotations

## Contributing

When contributing new dataset classes:
1. Follow the standard interface
2. Include comprehensive documentation
3. Add tests in `tests/test_datasetname.py`
4. Update requirements.txt if needed
5. Provide example usage