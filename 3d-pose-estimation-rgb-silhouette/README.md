# Pose Estimation Using Silhouette and Texture

This repository contains an implementation of **3D object pose estimation using differentiable rendering with both silhouette-based and texture-based losses**.

The project extends silhouette-only pose estimation by adding an RGB texture loss. The main goal is to analyze how silhouette information and texture information contribute to camera pose optimization.

## Author

**Madiyar Bolatov**  
AAI-2501M  
Astana IT University

## Project Overview

The objective of this project is to estimate the pose of a 3D object by optimizing camera parameters through a differentiable rendering pipeline.

Unlike silhouette-only approaches, this project combines:

- **Silhouette loss** for global shape alignment
- **Texture/RGB loss** for local appearance alignment

The experiments compare different loss-weight configurations to determine whether texture improves pose estimation or makes optimization less stable.

## Key Idea

A 3D object is rendered from a predicted camera pose. The rendered result is compared with a target image generated from a known camera pose.

The camera parameters are then optimized until the predicted render matches the target render.

The optimization uses a weighted loss:

```text
Loss = w_silh * L_silh + w_tex * L_tex
```

where:

- `L_silh` is the silhouette loss;
- `L_tex` is the RGB texture loss;
- `w_silh` controls the importance of silhouette alignment;
- `w_tex` controls the importance of texture alignment.

## Features

- 3D mesh loading using PyTorch3D
- Mesh normalization and decimation
- Synthetic texture generation
- RGB target rendering
- Silhouette target rendering
- Differentiable camera optimization
- Comparison of silhouette-only, texture-only, and combined losses
- Visualization of RGB results, silhouette results, and loss curves

## Object Used

<p align="center">
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/3d-pose-estimation-silhouette/figures/guitar.jpg" />
</p>

The object used in this project is a **3D guitar model** in `.obj` format.

Since the original model did not include a predefined texture, a synthetic texture was generated. Each triangle face of the mesh was assigned a random color using a texture atlas.

This made it possible to test the effect of RGB texture information while keeping the object simple and computationally manageable.

## Methodology

### 1. Data Preparation

The data preparation pipeline includes:

1. Loading the 3D guitar mesh using PyTorch3D
2. Simplifying the mesh using quadric decimation
3. Normalizing the object by centering and scaling it
4. Assigning a synthetic texture to each triangle
5. Rendering target RGB and silhouette images from a known camera pose

<p align="center">
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/3d-pose-estimation-silhouette/figures/norm.jpg" />
</p>

Mesh decimation was used to reduce computational cost while preserving the general geometry of the guitar.

### 2. Target Image Generation

Two target images were generated:

| Target Type | Purpose |
|---|---|
| RGB image | Used for texture-based loss |
| Silhouette image | Used for silhouette-based loss |

These target images serve as ground truth during camera pose optimization.

### 3. Camera Optimization

The differentiable rendering pipeline optimizes camera parameters:

```text
Distance
Elevation
Azimuth
```

At each optimization step:

1. The mesh is rendered from the current camera pose.
2. The rendered RGB and/or silhouette image is compared with the target.
3. The loss is computed.
4. Gradients are backpropagated through the renderer.
5. Camera parameters are updated.

## Loss Function

The total loss is defined as:

```text
Loss = w_silh * L_silh + w_tex * L_tex
```

where:

```text
L_silh = MSE(S_pred, S_target)
L_tex  = MSE(I_pred, I_target)
```

Definitions:

- `S_pred` — predicted silhouette
- `S_target` — target silhouette
- `I_pred` — predicted RGB render
- `I_target` — target RGB render

## Experiments

Four experiments were conducted with different loss weights.

| Experiment | Silhouette Weight | Texture Weight | Description |
|---|---:|---:|---|
| A | 1.0 | 0.0 | Silhouette-only optimization |
| B | 0.0 | 1.0 | Texture-only optimization |
| C | 1.0 | 0.1 | Combined loss with weak texture contribution |
| D | 1.0 | 0.3 | Combined loss with stronger texture contribution |

## Results

### Experiment A: Silhouette Only

<p align="center">
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/3d-pose-estimation-rgb-silhouette/figures/silh_only_rgb.jpg" />
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/3d-pose-estimation-rgb-silhouette/figures/silh_only_silh.jpg" />
</p>

```text
w_silh = 1.0
w_tex  = 0.0
```

Results:

- Stable convergence
- Correct object shape and pose
- Smooth loss decrease

Conclusion:

Silhouette provides strong global geometric information and is reliable for camera pose estimation.

### Experiment B: Texture Only

<p align="center">
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/3d-pose-estimation-rgb-silhouette/figures/text_only_rgb.jpg" />
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/3d-pose-estimation-rgb-silhouette/figures/text_only_silh.jpg" />
</p>

```text
w_silh = 0.0
w_tex  = 1.0
```

Results:

- Optimization became unstable
- Camera moved away from the object
- Rendered image became empty

Conclusion:

Texture alone is not sufficient for reliable pose estimation. It can mislead optimization because RGB appearance is sensitive to local color patterns, lighting, and alignment errors.

### Experiment C: Silhouette + Weak Texture

```text
w_silh = 1.0
w_tex  = 0.1
```

Results:

- Stable convergence
- Accurate pose reconstruction
- Good alignment of both shape and appearance

Conclusion:

This configuration achieved the best balance between geometry and detail.

### Experiment D: Silhouette + Stronger Texture

```text
w_silh = 1.0
w_tex  = 0.3
```

Results:

- Optimization still converged
- Result was slightly worse than the `w_tex = 0.1` experiment
- Higher texture contribution introduced noise into the optimization

Conclusion:

Texture can help, but only when its weight is small enough. If texture is weighted too strongly, it can reduce stability.

## Main Findings

The experiments show a clear difference between silhouette and texture information.

### Silhouette Loss

Silhouette loss:

- provides global shape information;
- is stable and reliable;
- guides camera optimization effectively;
- prevents degenerate empty-render solutions.

### Texture Loss

Texture loss:

- provides local appearance detail;
- is sensitive to color variation and lighting;
- can mislead optimization if used alone;
- works best as a small auxiliary term.

## Technologies Used

- Python
- PyTorch
- PyTorch3D
- NumPy
- Matplotlib
- Differentiable rendering
- Mesh processing
- Gradient-based optimization

## Possible Improvements

Future improvements could include:

- testing more complex textured objects;
- comparing different optimizers;
- using real RGB images instead of synthetic target renders;
- adding regularization to camera parameters;
- evaluating robustness under different lighting conditions;
- testing additional texture weights.

## Final Conclusion

This project demonstrates that silhouette loss is essential for accurate object pose estimation.

The best result was achieved by combining silhouette and texture losses, where silhouette dominates and texture provides additional fine-grained adjustment.

```text
Best configuration: w_silh = 1.0, w_tex = 0.1
```

The silhouette provides a robust global signal, while texture acts as an auxiliary component for improving visual alignment.

