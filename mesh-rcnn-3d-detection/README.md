# 3D Object Detection with Mesh R-CNN

This repository contains an experimental evaluation of **Mesh R-CNN for 3D object detection and mesh prediction**.

The project investigates how a pretrained Mesh R-CNN model performs on objects from familiar Pix3D categories and on objects outside its training distribution.

## Author

**Madiyar Bolatov**  
AAI-2501M  
Astana IT University

## Project Overview

Mesh R-CNN is a model for joint 2D object perception and 3D shape prediction. Unlike standard object detection models that predict only a class label, bounding box, or segmentation mask, Mesh R-CNN also predicts a 3D mesh for each detected object.

The goal of this project is to test the applicability limits of Mesh R-CNN by evaluating it on different object categories:

- an object from a Pix3D category;
- another object from a known Pix3D category;
- an object outside the Pix3D training categories.

This setup helps analyze how well the model works on familiar objects and how it behaves under out-of-distribution conditions.

## Objective

The main objectives of this project are:

- to run a pretrained Mesh R-CNN model on custom input images;
- to evaluate object detection confidence;
- to inspect the quality of predicted 3D meshes;
- to compare results on in-domain and out-of-domain objects;
- to understand the limitations of single-image 3D mesh prediction.

## Background

Mesh R-CNN extends Mask R-CNN by adding a 3D mesh prediction branch.

A standard Mask R-CNN model predicts:

- object class;
- bounding box;
- segmentation mask.

Mesh R-CNN adds:

- coarse voxel prediction;
- mesh conversion;
- mesh refinement using graph convolution layers.

The model first predicts a coarse 3D shape and then refines it into a triangle mesh. This allows the model to estimate approximate 3D object geometry from a single RGB image.

## Mesh R-CNN Pipeline

<p align="center">
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/mesh-rcnn-3d-detection/figures/mesh_r-cnn.jpg"/>
</p>

The general pipeline is:

```text
Input RGB Image
    ↓
Backbone Feature Extraction
    ↓
2D Object Detection
    ↓
Segmentation Mask Prediction
    ↓
Coarse Voxel Prediction
    ↓
Mesh Conversion
    ↓
Graph-based Mesh Refinement
    ↓
Predicted 3D Mesh
```

This makes Mesh R-CNN useful for tasks where both 2D detection and 3D shape estimation are required.

## Methodology

The project was implemented in a Jupyter Notebook inside WSL.

The pretrained Pix3D Mesh R-CNN model was used with the official demo script.

### Tools and Libraries

- Python 3.10
- PyTorch
- CUDA
- Detectron2
- PyTorch3D
- Mesh R-CNN
- Jupyter Notebook
- WSL

### Model Configuration

The model was launched using the official Mesh R-CNN demo script:

```bash
demo/demo.py
```

with the configuration file:

```bash
configs/pix3d/meshrcnn_R50_FPN.yaml
```

For familiar objects such as chair and sofa, the `--onlyhighest` flag was used to keep the highest-confidence prediction.

For the out-of-distribution object, the model was allowed to generate multiple predictions so that its behavior on unfamiliar categories could be analyzed.

## Selected Images

The experiment used images containing:

| Object | Category Type | Purpose |
|---|---|---|
| Chair | Familiar Pix3D-like category | Test in-domain performance |
| Sofa | Familiar Pix3D-like category | Test another known furniture category |
| Motorcycle / Car | Out-of-distribution object | Test generalization limits |

<p align="center">
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/synsin-novel-view-synthesis/figures/chair_sofa_car.jpg"/>
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/synsin-novel-view-synthesis/figures/chair_sofa_motorcycle.jpg"/>
</p>

The chair and sofa were selected because they are close to the Pix3D furniture domain. The motorcycle/car example was selected because it does not belong to the Pix3D furniture categories, making it useful for out-of-distribution testing.

## Results

### 1. Chair Image

For the chair image, the model predicted:

```text
Class: chair
Confidence score: 1.000
```

This was a correct semantic prediction.

The generated mesh captured the general chair-like structure, including:

- a large back/seat region;
- supporting leg-like parts;
- approximate global object shape.

However, the mesh quality was rough. Thin structures such as chair legs were distorted and incomplete.

#### Conclusion for Chair

Mesh R-CNN worked well for recognizing the chair, but the predicted mesh was only an approximate reconstruction. The model struggled with fine details and thin geometry.

### 2. Sofa Image

For the sofa image, the model predicted:

```text
Class: sofa
Confidence score: 1.000
```

This was also a correct semantic prediction.

The predicted mesh represented the general elongated shape of the sofa. The model captured the global volume, but the reconstruction was simplified.

Missing or weakly reconstructed details included:

- cushions;
- armrests;
- soft surface details;
- small legs.

#### Conclusion for Sofa

The model successfully recognized the sofa and recovered its approximate 3D structure. However, the predicted mesh lacked detailed geometry.

### 3. Out-of-Distribution Object

For the motorcycle/car image, the object was outside the Pix3D furniture domain.

The model behavior was less reliable because the object did not belong to the categories seen during training.

Observed behavior:

- predictions became less meaningful;
- the model could confuse unfamiliar objects with known categories;
- generated meshes did not accurately represent the actual object shape;
- reconstruction quality dropped significantly.

#### Conclusion for Out-of-Distribution Object

Mesh R-CNN does not generalize well to objects outside its training categories. This shows that the model is strongly dependent on the object categories and shapes present in the training dataset.

## Rendered Mesh Visualization

The raw `.obj` mesh prediction does not immediately show the reconstruction quality. Therefore, predicted meshes were rendered from multiple viewpoints using PyTorch3D.

This visualization step helped analyze whether the predicted 3D geometry preserved the main object structure.

For familiar objects such as chair and sofa, the rendered meshes showed approximate object shapes. However, details such as chair legs, sofa cushions, and thin structures were not reconstructed accurately.

## Main Findings

The experiment shows that Mesh R-CNN can recover approximate 3D shape for familiar Pix3D-like categories, but it has clear limitations.

### Strengths

Mesh R-CNN performed well in:

- recognizing familiar object categories;
- predicting high-confidence labels for chair and sofa;
- reconstructing approximate global object geometry;
- producing usable coarse 3D meshes for known categories.

### Limitations

Mesh R-CNN struggled with:

- fine object details;
- thin structures such as chair legs;
- soft or complex surfaces such as sofa cushions;
- objects outside the training distribution;
- accurate reconstruction from a single image.

## In-Domain vs Out-of-Domain Performance

| Case | Detection Quality | Mesh Quality | Overall Result |
|---|---|---|---|
| Chair | High | Approximate but rough | Good recognition, limited geometry |
| Sofa | High | Simplified shape | Good recognition, coarse reconstruction |
| Motorcycle / Car | Unstable | Poor | Weak generalization |

The model is reliable when the input object is close to Pix3D categories, but its predictions become unstable for unfamiliar categories.

## Technologies Used

- Python
- PyTorch
- CUDA
- Detectron2
- PyTorch3D
- Mesh R-CNN
- Pix3D pretrained model
- Jupyter Notebook
- WSL

## Possible Improvements

Future improvements could include:

- testing more Pix3D categories;
- evaluating more out-of-distribution objects;
- comparing Mesh R-CNN with newer 3D reconstruction models;
- using quantitative mesh metrics if ground-truth 3D models are available;
- improving visualization with more camera angles;
- testing higher-resolution input images;
- fine-tuning the model on additional object categories.

## Conclusion

This project demonstrates the strengths and limitations of Mesh R-CNN for 3D object detection and single-image mesh prediction.

The model performs well on familiar Pix3D-like categories such as chair and sofa, achieving correct object recognition and approximate 3D shape prediction.

However, the generated meshes are coarse and simplified, especially for thin or detailed structures. The model also struggles with objects outside its training distribution.

Overall, Mesh R-CNN is effective for coarse 3D reconstruction of known categories but has limited generalization to unfamiliar object types.

FOR MORE INFO, CHECK Report.pdf!!!