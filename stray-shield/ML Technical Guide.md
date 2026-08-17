# Stray Shield — Dog Health Classification Technical Guide

> Technical documentation for the machine-learning pipeline used to classify dog-health images in Stray Shield.

## 1. Overview

Stray Shield uses a lightweight machine-learning pipeline to provide an initial health classification of dogs from uploaded images.

The purpose of the model is to provide NGOs with an additional **triage signal** that can help prioritize reported cases. The classification is not intended to replace professional veterinary assessment.

The current classification pipeline uses:

* **TensorFlow.js** for machine-learning operations
* **MobileNet** for image feature extraction
* **KNN Classifier** for classification
* **Jimp** for image processing
* A labeled image dataset containing health-related categories

The current classes are:

```text id="r8s3dg"
healthy
sick
injured
```

---

# 2. Why Machine Learning Is Used

When citizens submit reports, an NGO may need to review a large number of images and cases.

A machine-learning-assisted classification step can provide a preliminary indication of the dog's apparent condition.

The intended workflow is:

```text id="2m2bkm"
Dog Image
    │
    ▼
Image Preprocessing
    │
    ▼
MobileNet Feature Extraction
    │
    ▼
KNN Classification
    │
    ▼
Health Classification
    │
    ▼
NGO Triage
```

The classification output can help responders identify cases that may require more immediate attention.

> **Important:** The model provides an automated classification signal only. It should not be treated as a veterinary diagnosis.

---

# 3. Machine-Learning Pipeline

The pipeline consists of four primary stages:

1. Image input
2. Image preprocessing
3. Feature extraction
4. KNN classification

```text id="8zv1l5"
Input Image
     │
     ▼
Jimp Image Processing
     │
     ▼
224 × 224 × 3 Tensor
     │
     ▼
MobileNet Embedding
     │
     ▼
KNN Classifier
     │
     ▼
healthy / sick / injured
```

---

# 4. Input

The frontend currently supports multiple image uploads when creating a report.

For the classification pipeline, images are processed **one at a time**.

Supported image formats include common formats such as:

```text id="l3o7gq"
.jpg
.jpeg
.png
```

If a report contains multiple images, the application can process each image individually and aggregate or display the resulting classifications as required by the application workflow.

---

# 5. Image Preprocessing

Before an image can be passed to MobileNet, it must be converted into a tensor with the expected dimensions and value range.

The preprocessing logic is implemented in:

```text id="0j3p4k"
ml/predict.js
```

The processing pipeline consists of the following steps.

---

## 5.1 Read the Image

Jimp is used to load the image and access its pixel data.

The image is initially represented as an RGBA bitmap:

```text id="d3kq3x"
Red
Green
Blue
Alpha
```

---

## 5.2 Remove the Alpha Channel

The classifier operates on three color channels.

The alpha channel is therefore removed:

```text id="5r0xiz"
RGBA → RGB
```

The resulting image contains:

```text id="yrw4dx"
Red
Green
Blue
```

---

## 5.3 Resize the Image

The image is resized to:

```text id="0u5t7q"
224 × 224 pixels
```

This provides a consistent input size for the MobileNet feature-extraction step.

---

## 5.4 Normalize Pixel Values

Pixel values are normalized to the range:

```text id="b6s5j1"
[0, 1]
```

This converts the original pixel representation into floating-point values suitable for TensorFlow.js processing.

---

## 5.5 Add the Batch Dimension

The image tensor is expanded to include a batch dimension.

The resulting tensor shape is:

```text id="o4h1h7"
[1, 224, 224, 3]
```

Where:

| Dimension | Meaning                       |
| --------: | ----------------------------- |
|       `1` | Number of images in the batch |
|     `224` | Image height                  |
|     `224` | Image width                   |
|       `3` | RGB color channels            |

The resulting tensor is ready for feature extraction.

---

# 6. Model Architecture

The classification system combines **MobileNet** and a **KNN classifier**.

```text id="k0z8x3"
                Input Image
                     │
                     ▼
              Image Preprocessing
                     │
                     ▼
                  MobileNet
                     │
                     │ Feature Embedding
                     ▼
              KNN Classifier
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
       Healthy      Sick      Injured
```

---

## 6.1 MobileNet

MobileNet is used as the feature extractor.

Instead of training an entire deep neural network from scratch, Stray Shield uses a pretrained MobileNet model to transform an image into a numerical representation known as an **embedding**.

The relevant operation in the implementation is based on:

```javascript id="c7i4zt"
mobilenet.infer(imageTensor, true)
```

The resulting embedding captures visual characteristics of the image that can be used by the downstream classifier.

### Why MobileNet?

MobileNet is useful for this project because it is relatively lightweight compared with larger deep-learning architectures.

This makes it suitable for:

* Small-scale image classification
* Faster inference
* TensorFlow.js environments
* Projects with limited training data
* Transfer-learning-style workflows

---

# 7. KNN Classifier

The second part of the pipeline is the TensorFlow.js KNN classifier:

```text id="m0t6w8"
@tensorflow-models/knn-classifier
```

The KNN classifier does not perform traditional neural-network training.

Instead, it stores labeled MobileNet embeddings and determines the class of a new image by comparing its embedding with previously stored examples.

Conceptually:

```text id="4py6qy"
Training Images
      │
      ▼
MobileNet
      │
      ▼
Feature Embeddings
      │
      ▼
KNN Classifier
      │
      ▼
Stored Labeled Examples
```

For a new image:

```text id="j7i2ny"
New Image
    │
    ▼
MobileNet Embedding
    │
    ▼
Compare Against Stored Embeddings
    │
    ▼
Nearest Neighbors
    │
    ▼
Predicted Class
```

---

# 8. Why MobileNet + KNN?

The combination provides a relatively lightweight classification approach.

Instead of training a large neural network specifically for the project:

1. MobileNet provides general-purpose visual features.
2. The project provides labeled examples.
3. KNN uses those examples to classify new images.

This approach can be useful when:

* The dataset is relatively small.
* Fast experimentation is required.
* A lightweight model is preferred.
* Full neural-network retraining is unnecessary.

However, the quality of the resulting classifier depends heavily on the quality and diversity of the labeled dataset.

---

# 9. Dataset Structure

The training dataset is organized into class-specific directories.

```text id="h19m5p"
dataset/
├── healthy/
├── sick/
└── injured/
```

Each directory contains images belonging to its corresponding class.

For example:

```text id="q5k4d7"
dataset/
├── healthy/
│   ├── dog01.jpg
│   ├── dog02.jpg
│   └── ...
│
├── sick/
│   ├── dog01.jpg
│   ├── dog02.jpg
│   └── ...
│
└── injured/
    ├── dog01.jpg
    ├── dog02.jpg
    └── ...
```

The directory structure determines the labels used by the classifier.

---

# 10. Training Process

The training workflow is implemented through:

```text id="i7k6q3"
ml/train.js
```

Because the project uses MobileNet embeddings with KNN, training is different from conventional neural-network training.

There is no requirement to retrain MobileNet itself.

Instead, the training process primarily builds the KNN classifier dataset.

## Training Workflow

```text id="y4q1wz"
Labeled Images
      │
      ▼
Read Image
      │
      ▼
Preprocess Image
      │
      ▼
MobileNet Embedding
      │
      ▼
Assign Class Label
      │
      ▼
Add Embedding to KNN
      │
      ▼
Save Classifier Dataset
```

### Steps

1. Place labeled images into the appropriate dataset directories.
2. Load each image.
3. Preprocess the image.
4. Generate a MobileNet embedding.
5. Associate the embedding with its corresponding class.
6. Add the labeled embedding to the KNN classifier.
7. Save the classifier dataset.

The resulting classifier data is stored under:

```text id="0y7vpl"
ml/health_model/
```

with the classifier dataset represented by:

```text id="x5g0yn"
classifier.json
```

---

# 11. Prediction Process

Prediction is implemented through:

```text id="cv6kpb"
ml/predict.js
```

The prediction workflow loads the trained classifier and applies the same preprocessing pipeline used during training.

```text id="c1w7mg"
New Image
    │
    ▼
Image Preprocessing
    │
    ▼
MobileNet Embedding
    │
    ▼
KNN predictClass()
    │
    ▼
Classification Result
```

Conceptually, the implementation performs:

```javascript id="d8v4xg"
const embedding = mobilenet.infer(imgTensor, true);

const result = classifier.predictClass(embedding);
```

The classifier result contains the predicted label and class confidence information.

---

# 12. Classification Categories

The current dataset contains three categories:

| Label     | Meaning                                                |
| --------- | ------------------------------------------------------ |
| `healthy` | Image is classified as belonging to the healthy class. |
| `sick`    | Image is classified as belonging to the sick class.    |
| `injured` | Image is classified as belonging to the injured class. |

Additional classes can be introduced by:

1. Creating a corresponding dataset directory.
2. Adding representative labeled images.
3. Updating the training workflow.
4. Rebuilding the classifier dataset.

For example:

```text id="d7w9up"
dataset/
├── healthy/
├── sick/
├── injured/
└── underweight/
```

The additional class would then need to be incorporated into the classifier dataset.

---

# 13. Backend Integration

The Express backend contains the classification API endpoint:

```text id="0d5z9e"
POST /api/classify
```

The current endpoint serves as an integration point for the ML pipeline.

The intended flow is:

```text id="w4f8j9"
Client
   │
   │ Image
   ▼
POST /api/classify
   │
   ▼
Image Preprocessing
   │
   ▼
MobileNet
   │
   ▼
KNN Classifier
   │
   ▼
Prediction
   │
   ▼
JSON Response
```

---

# 14. Integration Strategies

There are two potential approaches for integrating the ML pipeline with the API.

## Option 1 — Synchronous Classification

The model and classifier are loaded when the backend starts.

```text id="v6f3ax"
Server Startup
     │
     ├── Load MobileNet
     └── Load KNN Dataset
              │
              ▼
       API Request
              │
              ▼
        Run Prediction
              │
              ▼
        Return Result
```

### Advantages

* Simple request flow
* No separate processing infrastructure
* Fast after the models are loaded
* Straightforward API integration

### Considerations

* Prediction work occurs during the API request.
* Large images or multiple requests can increase CPU usage.
* The server must keep the ML models in memory.

---

## Option 2 — Asynchronous Classification

Classification can be moved to a background processing workflow.

```text id="4o5p7k"
API Request
    │
    ▼
Store Image / Job
    │
    ▼
Background Queue
    │
    ▼
ML Worker
    │
    ▼
Prediction
    │
    ▼
Store Result
```

### Advantages

* API requests remain responsive.
* ML workloads can be scaled independently.
* Better suited to larger workloads.
* Multiple workers can process predictions.

### Considerations

* Requires background-job infrastructure.
* Results are not necessarily available immediately.
* Adds architectural complexity.

For the current project scale, synchronous processing can be simpler. An asynchronous architecture becomes more useful as prediction volume increases.

---

# 15. Example Prediction Flow

The backend integration can conceptually follow this sequence:

```javascript id="d2y1ps"
const imgTensor = preprocess(imageBuffer);

const embedding = mobilenet.infer(
  imgTensor,
  true
);

const result = classifier.predictClass(
  embedding
);

return {
  label: result.label,
  confidences: result.confidences
};
```

The preprocessing function is responsible for converting the input image into the expected tensor format:

```text id="z9g1p3"
imageBuffer
     ↓
Jimp
     ↓
RGBA bitmap
     ↓
RGB
     ↓
224 × 224
     ↓
Normalize [0,1]
     ↓
[1,224,224,3]
     ↓
MobileNet
```

---

# 16. Model Loading

Prediction requires both the MobileNet model and the saved KNN classifier dataset.

Conceptually:

```text id="z7t8ko"
Application Startup
       │
       ├───────────────┐
       ▼               ▼
   MobileNet      classifier.json
       │               │
       └───────┬───────┘
               ▼
         Ready for Prediction
```

Loading the models once rather than on every request can reduce unnecessary initialization overhead.

For a production implementation, model loading should therefore generally occur during application initialization or through a cached model-loading mechanism.

---

# 17. Model Output

The KNN classifier can provide:

* Predicted class label
* Confidence information for the available classes

Conceptually:

```json id="lq0l5h"
{
  "label": "injured",
  "confidences": {
    "healthy": 0.05,
    "sick": 0.15,
    "injured": 0.80
  }
}
```

The exact response structure should match the final backend implementation.

> Confidence values should be treated as model outputs rather than clinical certainty.

---

# 18. Limitations

The current ML approach has several limitations.

## Dataset Size

KNN classification depends heavily on the examples stored in the classifier.

A small dataset may result in poor generalization.

```text id="h5y1z2"
Small / biased dataset
        ↓
Limited representation
        ↓
Potentially inaccurate classification
```

---

## Dataset Representativeness

The training images should represent the types of images expected in real-world reports.

Factors such as:

* Dog breed
* Age
* Body position
* Camera angle
* Lighting
* Background
* Distance from camera
* Visible injuries

can affect classification performance.

---

## Image Quality

Low-resolution, blurry, or heavily obstructed images may reduce prediction reliability.

Examples include:

* Poor lighting
* Motion blur
* Partial occlusion
* Very distant subjects
* Multiple dogs in one image
* Images where the dog is not clearly visible

---

## Generic Feature Extraction

MobileNet is a general-purpose image model rather than a model specifically trained for stray-dog health assessment.

Its embeddings may therefore not capture every domain-specific characteristic required for reliable health classification.

---

## KNN Limitations

KNN prediction quality depends on the similarity between the new image's embedding and the stored training examples.

If a new image is significantly different from the training data, classification accuracy may decrease.

---

# 19. Improving Classification Accuracy

Several improvements could be considered as the project evolves.

### Expand the Dataset

Add more representative images for each class.

```text id="g9m1q8"
healthy  → More diverse healthy examples
sick     → More diverse sick examples
injured  → More diverse injury examples
```

### Improve Dataset Quality

Ensure that training images are:

* Correctly labeled
* Representative of real-world inputs
* Sufficiently diverse
* Free from unnecessary duplicates

### Data Augmentation

Introduce transformations such as:

* Rotation
* Cropping
* Scaling
* Flipping
* Brightness adjustments

This can increase variation in the training data.

### Higher-Capacity Models

If project requirements increase, a more specialized or higher-capacity model could replace the current MobileNet + KNN approach.

### Domain-Specific Training

A model trained specifically on dog-health imagery could potentially perform better than a generic feature extractor.

However, this would require a larger, carefully labeled dataset and a more substantial training workflow.

---

# 20. Performance Considerations

TensorFlow.js inference can consume significant CPU resources depending on the environment and workload.

Performance can be affected by:

* Image resolution
* Number of concurrent requests
* Model loading strategy
* CPU availability
* Number of predictions
* Whether predictions are performed synchronously

Potential improvements include:

* Loading models once during startup
* Caching model instances
* Resizing images before processing
* Processing predictions asynchronously
* Using TensorFlow.js Node optimizations where appropriate
* Scaling ML workers independently from the API

---

# 21. Security Considerations

Because the ML endpoint accepts image data, input validation is important.

The backend should validate:

* File type
* File size
* Image dimensions
* Request format
* Authentication
* Authorization

The API should also avoid accepting arbitrarily large payloads.

The current backend configuration includes an approximately **50 MB request-body limit**, which should be reviewed before production deployment.

Uploaded images should also be handled using secure storage practices rather than exposing arbitrary filesystem paths.

---

# 22. Current Implementation vs. Future Integration

The current project contains the core ML pipeline for preprocessing, MobileNet feature extraction, KNN classification, training, and prediction.

However, the `/api/classify` backend endpoint currently acts as an integration point rather than a complete production classification service.

| Component                          | Status                     |
| ---------------------------------- | -------------------------- |
| Image preprocessing                | ✅ Implemented              |
| MobileNet feature extraction       | ✅ Implemented              |
| KNN classifier                     | ✅ Implemented              |
| Training workflow                  | ✅ Implemented              |
| Prediction workflow                | ✅ Implemented              |
| Health dataset                     | ✅ Present                  |
| `/api/classify` endpoint           | ⚠️ Integration/placeholder |
| Automatic report classification    | 🔄 Future improvement      |
| Background ML processing           | 🔄 Future improvement      |
| Production-scale ML infrastructure | 🔄 Future improvement      |

This distinction is important because the ML pipeline and API integration are separate parts of the system.

---

# 23. End-to-End ML Workflow

The complete intended workflow is:

```text
                  ┌──────────────────┐
                  │  Citizen Upload  │
                  │      Image       │
                  └────────┬─────────┘
                           │
                           ▼
                  ┌──────────────────┐
                  │   Preprocessing  │
                  │                  │
                  │ Jimp             │
                  │ RGB conversion   │
                  │ Resize 224×224   │
                  │ Normalize        │
                  └────────┬─────────┘
                           │
                           ▼
                  ┌──────────────────┐
                  │     MobileNet    │
                  │                  │
                  │ Feature          │
                  │ Extraction       │
                  └────────┬─────────┘
                           │
                           ▼
                  ┌──────────────────┐
                  │  KNN Classifier  │
                  │                  │
                  │ Compare against  │
                  │ labeled examples │
                  └────────┬─────────┘
                           │
                           ▼
              ┌─────────────────────────┐
              │ Classification Result   │
              │                         │
              │ healthy / sick / injured│
              └────────────┬────────────┘
                           │
                           ▼
                  ┌──────────────────┐
                  │   NGO Triage     │
                  │   & Response     │
                  └──────────────────┘
```

---

# 24. Key Files

The machine-learning implementation is primarily organized under:

```text id="xj4p1z"
ml/
├── train.js
├── predict.js
└── health_model/
    └── classifier.json
```

### `ml/train.js`

Responsible for constructing the KNN classifier dataset from labeled images.

### `ml/predict.js`

Responsible for loading the classifier and performing image prediction.

### `ml/health_model/classifier.json`

Contains the saved classifier dataset generated from labeled training examples.

### `dataset/`

Contains the labeled source images used to construct the classifier dataset.

```text id="t1h4sn"
dataset/
├── healthy/
├── sick/
└── injured/
```

---

# 25. Summary

The Stray Shield ML pipeline uses a **MobileNet + KNN architecture** to provide lightweight image-based dog-health classification.

The workflow combines:

```text id="e6j4x8"
Image
  ↓
Jimp preprocessing
  ↓
224 × 224 × 3 tensor
  ↓
MobileNet embedding
  ↓
KNN classification
  ↓
Health category
```

The approach avoids expensive end-to-end neural-network training and instead uses pretrained visual features with a project-specific KNN classifier.

This makes the approach practical for experimentation with a relatively small dataset while keeping the architecture lightweight.

However, classification quality depends heavily on **dataset size, labeling quality, image diversity, and similarity between real-world images and training examples**.

Future development can improve the system through larger datasets, data augmentation, domain-specific models, asynchronous processing, and tighter integration between the ML pipeline and the report-management workflow.

> **The ML component is designed as a decision-support mechanism for NGO triage, not as a substitute for professional veterinary diagnosis.**
