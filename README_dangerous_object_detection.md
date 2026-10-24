# Dangerous Object Detection System

**Real-time object detection for three classes: knives (`Dao`), guns
(`Sung`), and scissors (`Keo`), using YOLOv8 and a webcam.**

## Overview

This project trains a YOLOv8 Nano object-detection model to detect three
categories of potentially dangerous objects:

-   **Dao** --- knives
-   **Sung** --- guns
-   **Keo** --- scissors

The notebook contains two main stages:

1.  **Model training:** Loads pretrained `yolov8n.pt`, configures the
    dataset, and fine-tunes the model for 50 epochs.
2.  **Real-time webcam detection:** Loads a trained `best.pt`
    checkpoint, reads frames from the default webcam, and runs object
    detection with a confidence threshold of `0.5`.

This is a computer-vision project intended for learning and
experimentation. Detection results can be incorrect, and the system
should not be treated as a reliable security or threat-assessment
solution.

## Features

-   Detects three configured classes: knives, guns, and scissors.
-   Uses the pretrained YOLOv8 Nano model as the starting point.
-   Trains with image size `640`, batch size `16`, and `50` epochs.
-   Configures the dataset with separate training, validation, and test
    image folders.
-   Runs inference on live webcam frames.
-   Filters predictions using a confidence threshold of `0.5`.
-   Allows the webcam window to be closed by pressing `q`.

## Technologies

  -----------------------------------------------------------------------
  Technology                          Purpose
  ----------------------------------- -----------------------------------
  Python                              Main programming language

  Ultralytics YOLO                    Model training and object detection

  YOLOv8 Nano (`yolov8n.pt`)          Pretrained object-detection model

  OpenCV (`cv2`)                      Webcam capture and video-frame
                                      handling

  PyYAML (`yaml`)                     Writes the dataset configuration to
                                      YAML

  NVIDIA GPU / CUDA                   GPU inference and training when the
                                      environment is configured correctly
  -----------------------------------------------------------------------

## System Architecture

``` text
Dataset
  |
  v
dataset_config.yaml
  |
  v
Pretrained YOLOv8 Nano (yolov8n.pt)
  |
  v
Fine-tune for 50 epochs
  |
  v
best.pt
  |
  v
Load trained model
  |
  v
Capture webcam frames with OpenCV
  |
  v
YOLO prediction (confidence threshold = 0.5)
  |
  v
Display detections
```

## Dataset Structure

The training code configures the dataset root as:

``` text
D:\CPV301\Project\dataset full
```

The expected folder layout is:

``` text
dataset full/
├── images/
│   ├── train/
│   ├── val/
│   └── test/
├── labels/
│   ├── train/
│   ├── val/
│   └── test/
└── ...
```

The `images/` paths are explicitly configured in the notebook. The
`labels/` layout above reflects the conventional YOLO dataset format and
should be verified against the actual dataset. Each image needs a
corresponding annotation file in YOLO format for training.

The class configuration in the notebook is:

``` yaml
nc: 3
names:
  - Dao
  - Sung
  - Keo
```

The class names and annotation class IDs must match consistently
throughout the dataset. The notebook does not include the dataset
itself.

## Training Configuration

The training cell creates `dataset_config.yaml` and uses the following
settings:

  ------------------------------------------------------------------------
  Parameter                                    Value Meaning
  --------------------- ---------------------------- ---------------------
  Base model                            `yolov8n.pt` Pretrained YOLOv8
                                                     Nano checkpoint

  Epochs                                        `50` Number of training
                                                     epochs

  Image size                                   `640` Input image size used
                                                     for training

  Batch size                                    `16` Number of images per
                                                     batch

  Device                                         `0` Requests GPU device 0

  Project output folder                      `Do_An` Ultralytics training
                                                     output directory

  Run name                         `Mo_Hinh_Cua_Toi` Name of the training
                                                     run
  ------------------------------------------------------------------------

The training script prints the expected checkpoint location as:

``` text
Do_An/Mo_Hinh_Cua_Toi/weights/best.pt
```

Ultralytics may automatically create a suffixed run directory (for
example, `Mo_Hinh_Cua_Toi2`) if the configured run name already exists,
depending on the run settings and existing files. Check the actual
output directory after training.

## Installation

Use a Python environment on a computer with a webcam. For GPU training,
install a PyTorch build compatible with the installed NVIDIA driver and
CUDA environment.

Install the Python packages:

``` bash
pip install ultralytics opencv-python PyYAML
```

The Ultralytics package manages the YOLO training and prediction API. A
compatible PyTorch installation is also required; follow the official
PyTorch installation guidance for the machine's CUDA setup.

## How to Run

### 1. Prepare the dataset

1.  Place the dataset in the directory configured in the training
    script, or update `data_config['path']` to the correct dataset root.
2.  Confirm that the training, validation, and test image folders exist.
3.  Confirm that YOLO-format label files are present and that class IDs
    correspond to `Dao`, `Sung`, and `Keo`.

### 2. Train the model

Run the training notebook cell or save the training code as a Python
script and execute it:

``` bash
python train.py
```

The example command assumes the training cell has been saved as
`train.py`. The code creates `dataset_config.yaml`, loads `yolov8n.pt`,
and begins training. The trained weights are saved in the Ultralytics
run directory.

If GPU device `0` is unavailable, update `device=0` to a supported
device setting (for example, CPU mode) as appropriate for the installed
Ultralytics version and environment.

### 3. Configure the checkpoint path

Before running webcam detection, update `duong_dan_model` in the
inference code so that it points to the actual `best.pt` produced by
training.

The training cell's configured output is:

``` text
Do_An/Mo_Hinh_Cua_Toi/weights/best.pt
```

The inference cell currently points to a different absolute path:

``` text
D:\CPV301\Project\dataset full\runs\detect\Do_An\Mo_Hinh_Cua_Toi-2\weights\best.pt
```

**These paths do not match exactly.** Confirm the real location of the
trained checkpoint and edit `duong_dan_model` accordingly. The inference
script will fail to load the model if the path is incorrect.

### 4. Start webcam detection

Run the inference cell after setting the checkpoint path. The program
opens the default webcam (`cv2.VideoCapture(0)`), processes each
captured frame, and displays the YOLO detection window.

-   `conf=0.5`: predictions below the configured confidence threshold
    are filtered out.
-   `device=0`: requests GPU device 0.
-   Press **`q`** to exit the loop.
-   The program releases the camera and closes OpenCV windows when the
    loop ends.

If the webcam cannot be opened, check camera permissions, close other
applications using the camera, and verify that camera index `0` is
correct.

## Project Structure

A possible repository layout is:

``` text
dangerous-object-detection/
├── README.md
├── train.py                 # YOLOv8 training script (if extracted from notebook)
├── detect_webcam.py         # Webcam inference script (if extracted from notebook)
├── dataset_config.yaml      # Generated by the training code
├── weights/
│   └── best.pt              # Trained checkpoint (not included by default)
└── dataset/                 # Dataset (not included by default)
    ├── images/
    └── labels/
```

The uploaded notebook currently contains two code cells rather than
separate `train.py` and `detect_webcam.py` files. The layout above is a
suggested organization for a GitHub repository, not a claim about the
uploaded file's current structure.

## Evaluation

The provided code does not print or record evaluation metrics in the
shown cells. Before presenting model performance, evaluate the trained
model on an independent test set and report appropriate metrics such as
precision, recall, mAP@0.5, and mAP@0.5:0.95. Do not infer model
accuracy from the fact that training completed successfully.

## Limitations and Future Improvements

-   **False positives and false negatives:** Objects may be missed or
    incorrectly classified due to lighting, occlusion, camera quality,
    distance, or dataset limitations.
-   **Dataset quality:** Performance depends on annotation accuracy,
    class balance, and how closely the dataset resembles real-world
    camera footage.
-   **Real-time performance:** FPS and latency depend on the GPU, image
    size, and runtime environment; they are not measured by the current
    code.
-   **Checkpoint paths:** The training and inference cells currently use
    different checkpoint paths, which should be reconciled before
    running the full workflow.
-   **Testing:** Add validation of the webcam input and a clear error
    when the checkpoint file cannot be found.
-   **Reproducibility:** Record dependency versions, training settings,
    dataset provenance, and evaluation results.
-   **Deployment:** Test carefully under varied environments before
    considering any real-world use.

## Responsible Use

This model is a prototype for object detection, not a definitive
judgment that a person or situation is dangerous. It may misidentify
ordinary objects or fail to detect relevant objects. Do not use its
predictions as the sole basis for security, disciplinary, or
law-enforcement decisions. Follow applicable laws and privacy
requirements when recording or processing camera footage.

## Acknowledgements

This project uses the Ultralytics YOLO framework and a pretrained YOLOv8
Nano model. Follow the applicable software and model licenses and terms
of use.
