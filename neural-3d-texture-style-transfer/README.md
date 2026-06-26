# Neural Texture Style Transfer on a 3D Mesh with Geometry Preservation

This repository contains an implementation of **neural texture style transfer on a 3D mesh while preserving the original geometry**.

The project applies artistic style transfer directly to the **UV texture map** of a 3D object. Unlike ordinary 2D style transfer, where each rendered image is stylized independently, this method optimizes the texture attached to the mesh. As a result, the stylized appearance becomes part of the 3D object and remains consistent from different camera viewpoints.

## Author

**Madiyar Bolatov**    
AAI-2501M  
Astana IT University

## Project Overview

The goal of this project is to stylize a textured 3D mesh while keeping its geometry unchanged.

<p align="center">
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/neural-3d-texture-style-transfer/figures/orig_mesh_chair.jpg"/>
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/neural-3d-texture-style-transfer/figures/orig_texture.jpg"/>
</p>

<img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/neural-3d-texture-style-transfer/figures/style.jpg"/>

A chair model from **ShapeNet** was used as the 3D object, and **Edvard Munch's The Scream** was used as the reference style image.

The main idea is:

```text
Keep mesh geometry fixed
Optimize only the UV texture map
Render the object from multiple viewpoints
Apply neural style loss to the rendered views
Update the texture through differentiable rendering
```

This allows the final object to preserve its original shape while receiving a new artistic texture.

## Objective

The main objective is to obtain a new UV texture map for a given 3D mesh so that the rendered object visually follows the target artistic style while preserving the recognizable structure and geometry of the original model.

The project includes:

- loading a textured 3D mesh with UV coordinates;
- rendering the mesh from several camera viewpoints;
- optimizing the UV texture map using differentiable rendering;
- applying neural style loss based on Gram matrices;
- using total variation regularization to reduce texture noise;
- comparing the UV-based method with naive 2D style transfer.

## Dataset and Input Data

The 3D model was taken from the **ShapeNet** dataset.

Selected category:

```text
03001627 - chair
```

The model files included:

| File | Description |
|---|---|
| `model_normalized.obj` | Mesh geometry, faces, and UV coordinates |
| `model_normalized.mtl` | Material file linked to the texture |
| `untitled/texture.jpg` | Original texture image |

The original texture was a wooden texture, which made the chair appear as a wooden object before stylization.

The style image used in this project was:

```text
Edvard Munch - The Scream
```

## Methodology

### 1. Mesh Representation

The 3D object is represented as a textured mesh:

```text
M = (V, F, U, T)
```

where:

- `V` is the set of vertices;
- `F` is the set of triangular faces;
- `U` is the set of UV coordinates;
- `T` is the UV texture map.

During optimization:

```text
V = constant
F = constant
T = optimized
```

This means that the mesh geometry is not changed. Only the texture map is updated.

### 2. Differentiable Rendering

The mesh is rendered from different camera viewpoints using **PyTorch3D**.

For each camera viewpoint, the renderer produces an image of the current textured mesh. Because the renderer is differentiable, gradients can flow from the rendered image back to the UV texture map.

This makes it possible to optimize the texture directly using neural style loss.

### 3. Multi-view Optimization

Several camera viewpoints are sampled during optimization. This is important because a texture that looks good from only one camera angle may not work well from other angles.

The project used a mixed viewpoint strategy:

- fixed views for stable coverage;
- random views for broader UV map optimization.

This helped more UV regions receive useful gradients.

### 4. Foreground Crop Using Alpha Mask

One problem in the initial implementation was that the object occupied only a small part of the rendered image, while most of the image was white background.

<img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/neural-3d-texture-style-transfer/figures/foreground_crop.jpg"/>

If style loss is computed over the full image, the background dominates the VGG feature comparison and weakens the style signal.

To solve this, the alpha channel from PyTorch3D rendering was used to crop the foreground object. The style loss was then computed mainly on the chair region instead of the full background.

### 5. Ambient-only Lighting

The first versions used Phong-style lighting, but shadows and highlights reduced the visibility of the stylized texture.

The final implementation used **ambient-only lighting**, which displayed texture colors more directly and made the stylized result more visible.

## Neural Style Transfer Loss

### VGG19 Feature Extraction

The style loss is computed using a pretrained **VGG19** network.

Both the rendered chair image and the style image are passed through VGG19. Feature maps are extracted from several convolutional layers.

The style layers used in the project were:

```text
relu1_1
relu2_1
relu3_1
relu4_1
relu5_1
```

### Gram Matrix

The style representation is based on Gram matrices. A Gram matrix captures correlations between feature channels and represents texture/style information rather than exact object structure.

This makes it suitable for transferring artistic texture patterns from the style image onto the chair surface.

### Style Loss

The style loss compares the Gram matrices of the cropped rendered object and the style image.

The goal is to make the rendered chair texture statistically similar to the target style image.

### Total Variation Regularization

Total variation regularization was applied to the UV texture map to reduce excessive noise.

It encourages neighboring texture pixels to have similar values, which produces smoother and more visually stable results.

## UV Coverage Map

A UV coverage map was computed to diagnose which UV pixels receive gradients during rendering.

<img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/neural-3d-texture-style-transfer/figures/uv_coverage_map.jpg"/>

The idea is:

```text
If a UV pixel receives gradient, this texture region contributes to at least one rendered view.
```

If large parts of the UV texture map have zero coverage, those areas cannot be optimized effectively from the selected camera viewpoints.

This diagnostic step helped analyze whether artifacts were caused by poor UV visibility or insufficient viewpoint coverage.

## Comparison with Naive 2D Style Transfer

The UV-based method was compared with a naive 2D style transfer baseline.

<img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/dev/neural-3d-texture-style-transfer/figures/comparison.jpg"/>

### Naive 2D Baseline

In the 2D baseline:

1. The object is rendered from several viewpoints.
2. Neural style transfer is applied independently to each rendered image.

This approach can produce stronger visual effects in each individual image, but it has a major limitation:

```text
Each viewpoint is stylized independently.
```

Therefore, the result is not 3D-consistent. The same part of the chair may look different from different views.

### UV-based Method

In the UV-based method:

1. A single UV texture map is optimized.
2. The same texture is used for all rendered views.
3. The stylized appearance remains attached to the 3D object.

This produces better multi-view consistency because the object has one shared stylized texture.

## Results

The experiment showed that UV texture optimization through differentiable rendering can transfer an artistic style to a 3D object while preserving its geometry.

Main observations:

- The geometry of the chair remained unchanged.
- The UV texture map was successfully modified.
- The stylized texture stayed consistent across different views.
- Foreground cropping improved optimization by reducing background influence.
- Ambient-only lighting made texture colors more visible.
- Mixed fixed/random viewpoints improved UV coverage.
- Some color artifacts and stripe-like patterns remained because of the UV layout.

## Limitations

The project has several limitations:

- the optimized texture contains visible artifacts and color stripes;
- some UV regions receive stronger gradients than others;
- the result depends strongly on the original UV layout;
- Gram matrix style loss does not preserve semantic structure from the style image;
- the UV-based result is less visually aggressive than independent 2D stylization;
- differentiable rendering is computationally more expensive than simple image-based stylization.

Despite these limitations, the main goal was achieved: the style was transferred to the UV texture map while the 3D geometry was preserved.

## Main Findings

The project demonstrates that:

- differentiable rendering can connect 2D neural style loss with 3D texture optimization;
- optimizing the UV texture map preserves geometry;
- UV-based stylization gives better view consistency than independent 2D style transfer;
- VGG19 Gram matrix loss can transfer artistic texture statistics to a 3D object;
- foreground cropping and lighting choices strongly affect optimization quality.

## Technologies Used

- Python
- PyTorch
- PyTorch3D
- VGG19
- Neural style transfer
- Differentiable rendering
- UV texture optimization
- Gram matrix style loss
- Total variation regularization
- ShapeNet 3D model

## Repository Structure

```text
.
├── README.md
├── notebooks/
│   └── neural_texture_style_transfer_3d.ipynb
├── data/
│   ├── model_normalized.obj
│   ├── model_normalized.mtl
│   └── untitled/
│       └── texture.jpg
├── style/
│   └── scream.jpg
├── outputs/
│   ├── original_chair.png
│   ├── original_uv_texture.png
│   ├── style_image.png
│   ├── foreground_crop.png
│   ├── optimized_uv_texture.png
│   ├── uv_coverage_map.png
│   └── final_stylized_views.png
└── requirements.txt
```

The repository structure can be adjusted depending on the actual files included in the project.

## How to Run

### 1. Clone the repository

```bash
git clone https://github.com/your-username/neural-texture-style-transfer-3d.git
cd neural-texture-style-transfer-3d
```

### 2. Install dependencies

```bash
pip install torch torchvision numpy matplotlib pillow
```

Install PyTorch3D according to your Python, PyTorch, and CUDA versions.

```bash
pip install pytorch3d
```

If this command does not work, follow the official PyTorch3D installation guide for your environment.

### 3. Add input files

Place the ShapeNet chair model and texture files in the `data/` directory:

```text
data/model_normalized.obj
data/model_normalized.mtl
data/untitled/texture.jpg
```

Place the style image in the `style/` directory:

```text
style/scream.jpg
```

### 4. Run the notebook

```bash
jupyter notebook notebooks/neural_texture_style_transfer_3d.ipynb
```

## Possible Improvements

Future improvements could include:

- using a cleaner UV layout;
- testing more ShapeNet categories;
- improving UV coverage through better camera sampling;
- adding semantic-aware style transfer;
- using stronger texture regularization;
- comparing different style images;
- improving rendering resolution;
- applying the method to real scanned 3D objects.

## Conclusion

This project implemented neural texture style transfer on a 3D mesh using UV map optimization and differentiable rendering.

A ShapeNet chair model was rendered from multiple viewpoints, and its UV texture map was optimized using VGG19-based style loss and total variation regularization.

The final result showed that artistic style can be transferred onto a 3D object surface while preserving the original mesh geometry. Compared with naive 2D style transfer, the UV-based method is more consistent across viewpoints because it creates a single shared stylized texture map.

Overall, the project demonstrates the integration of differentiable rendering, neural style transfer, and texture optimization for 3D mesh stylization.

FOR MORE INFO, CHECK Report.pdf!!!