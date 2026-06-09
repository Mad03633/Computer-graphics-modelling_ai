# Object Pose Estimation from Silhouette using Differentiable Rendering

This repository contains an implementation of **object pose estimation using silhouette-based differentiable rendering**.  
The project estimates the camera position of a 3D object by optimizing the difference between a rendered silhouette and a target silhouette.

The main idea is to use differentiable rendering so that gradients can flow through the rendering pipeline and update camera parameters directly.

## Author

**Madiyar Bolatov**  
AAI-2501M  
Astana IT University

## Project Overview

The goal of this project is to estimate the pose of a 3D object using only binary silhouette information.  
Instead of relying on RGB texture, feature matching, or manual keypoints, the method compares the predicted silhouette with a target silhouette and optimizes the camera position until both silhouettes align.

This project demonstrates that silhouette information can be sufficient for accurate pose recovery when combined with a differentiable renderer.

## Key Features

- 3D object loading from an `.obj` file
- Mesh normalization and decimation
- Silhouette rendering using PyTorch3D
- RGB rendering for visualization
- Camera position optimization
- Mean Squared Error loss between target and predicted silhouettes
- Adam-based gradient optimization
- Visual comparison of initial, optimized, and target silhouettes

## Methodology

### 1. Object Preparation

A custom 3D guitar model was used instead of the default teapot model from the tutorial.

<p align="center">
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/3d-pose-estimation-silhouette/figures/guitar.jpg" />
</p>

The object preparation included:

- loading the `.obj` mesh;
- applying a simple uniform gray texture;
- centering the mesh at the origin;
- scaling the mesh to fit inside a unit sphere;
- simplifying the mesh using decimation.

Mesh normalization was important because an incorrectly scaled object could produce unstable silhouettes or poor gradients during optimization.

### 2. Rendering Setup

Two renderers were used:

| Renderer | Purpose |
|---|---|
| `SoftSilhouetteShader` | Main renderer for silhouette optimization |
| `HardPhongShader` | RGB visualization only |

Main rendering parameters:

```text
image_size = 128
sigma = 1e-4
faces_per_pixel = 10
```

The silhouette image was extracted from the alpha channel of the rendered output.

### 3. Camera Model

The camera was represented by its 3D position:

```text
c = (x, y, z)
```

The rotation and translation matrices were computed using a look-at transformation.  
During optimization, the camera always points toward the center of the object.

### 4. Optimization Objective

The objective is to minimize the difference between the predicted silhouette and the target silhouette:

```text
Loss = MSE(S_pred, S_target)
```

where:

- `S_pred` is the predicted silhouette rendered from the current camera position;
- `S_target` is the target silhouette rendered from the known camera position.

The optimization was performed using the Adam optimizer.

## Experimental Setup

The target and initial camera positions were:

```text
Target camera position:  (0.0, 0.0, 2.0)
Initial camera position: (2.5, -1.5, 3.0)
```

The initial camera position was intentionally selected far from the target camera position to test whether the optimization process could recover the correct pose.

## Results

The loss decreased rapidly from approximately:

```text
0.0436 → less than 0.0001
```

This indicates stable convergence.

The optimized camera position was:

```text
Optimized camera position: (0.0051, 0.0041, 1.9989)
```

Compared with the target camera position:

```text
Target camera position:    (0.0, 0.0, 2.0)
```

The final error was very small, showing that the pose was recovered accurately.

## Visual Results

The visual comparison showed that:

1. The initial silhouette was small and misaligned.
2. The optimized silhouette became nearly identical to the target silhouette.
3. The RGB visualization confirmed that the final camera pose was correctly aligned with the object.

## Challenges and Solutions

### Mesh Scale Issues

Without normalization, the mesh could appear too large or too small in the rendered image.  
This caused unstable gradients and incorrect silhouette matching.

**Solution:**  
The mesh was centered and scaled to fit inside a unit sphere.

### Camera Initialization

If the camera was too far from the object, the object became barely visible.  
If the camera was too close, parts of the object were clipped.

**Solution:**  
A reasonable but intentionally misaligned initial camera position was selected.

### Renderer Parameters

Very sharp silhouettes can produce unstable gradients, while low rendering quality can reduce optimization accuracy.

**Solution:**  
The renderer was configured with:

```text
sigma = 1e-4
faces_per_pixel = 10
```

This provided a balance between silhouette sharpness and stable gradients.

## Technologies Used

- Python
- PyTorch
- PyTorch3D
- Matplotlib
- NumPy
- Adam optimizer
- Differentiable rendering

## Conclusion

This project successfully demonstrates pose estimation using only silhouette information and differentiable rendering.

The method achieved:

- accurate camera position recovery;
- stable and fast convergence;
- robustness without relying on texture or RGB information.

The results confirm that silhouette-based optimization is an effective approach for object pose estimation when the object geometry is known and a differentiable rendering pipeline is available.

FOR MORE INFO, CHECK Report.pdf!!!
