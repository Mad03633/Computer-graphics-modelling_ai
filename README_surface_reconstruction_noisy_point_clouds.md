# Surface Reconstruction from Noisy Point Clouds

This project implements surface reconstruction from a noisy 3D point cloud using a deformable mesh model in PyTorch3D. The goal is to study how different regularization terms affect the quality of the reconstructed mesh.

The reconstruction starts from an initial spherical mesh and gradually deforms its vertices to fit a noisy point cloud sampled from a custom 3D object.

## Project Overview

Surface reconstruction is an important task in 3D computer vision. It aims to recover a smooth and meaningful 3D surface from a set of points. In real-world scenarios, point clouds are often noisy because of sensor limitations, scanning errors, or imperfect data acquisition.

In this project, a noisy point cloud is generated from a sofa-like 3D object. A deformable mesh is then optimized to match the point cloud while maintaining a reasonable surface structure.

## Objectives

The main objectives of this project are:

- Reconstruct a smooth 3D mesh from a noisy point cloud.
- Use PyTorch3D for point cloud sampling, mesh deformation, and loss computation.
- Compare different loss function configurations.
- Analyze the effect of regularization on the final mesh quality.
- Identify the best balance between geometric accuracy and smoothness.

## Dataset and Input Data

A custom 3D sofa-like object was used instead of the default tutorial object.

The data preparation pipeline includes:

1. Loading a 3D object from an `.obj` file.
2. Sampling 5,000 points from the object surface.
3. Adding Gaussian noise to simulate real-world scanning conditions.
4. Normalizing the point cloud to zero mean and unit scale.

Noise configuration:

```text
Noise type: Gaussian
Standard deviation: 0.01
```

## Methodology

### 1. Initial Mesh

The reconstruction begins from a spherical mesh:

```python
src_mesh = ico_sphere(5, device)
```

The parameter `5` controls the subdivision level of the sphere and determines the resolution of the initial mesh.

### 2. Mesh Deformation

The vertices of the initial mesh are updated using gradient-based optimization. The learnable variable is the vertex offset, which gradually deforms the sphere toward the target point cloud.

Training setup:

```text
Optimizer: SGD
Iterations: 2000
Deformation variable: vertex offsets
```

### 3. Loss Function

The total loss combines three terms:

```text
L = wc * L_chamfer + we * L_edge + wl * L_laplacian
```

Where:

- `L_chamfer` measures the distance between the predicted mesh and the target point cloud.
- `L_edge` encourages reasonable edge lengths and prevents irregular mesh deformation.
- `L_laplacian` encourages smoothness of the reconstructed surface.

## Experiments

Three experiments were conducted with different loss weights.

### Experiment A: No Regularization

```text
w_chamfer = 1.0
w_edge = 0.0
w_laplacian = 0.0
```

This setup uses only Chamfer distance. It achieves the lowest numerical loss, but the resulting mesh becomes noisy and unstable because there is no smoothness constraint.

### Experiment B: Strong Smoothness

```text
w_chamfer = 1.0
w_edge = 0.0
w_laplacian = 10.0
```

This setup produces the smoothest mesh, but it removes important geometric details. The result becomes overly regularized and does not preserve the original object shape well.

### Experiment C: Balanced Regularization

```text
w_chamfer = 1.0
w_edge = 0.5
w_laplacian = 0.05
```

This setup provides the best compromise between surface fidelity and smoothness. It keeps the global shape of the object while reducing noise and instability.

## Results

The experiments showed that regularization plays a critical role in surface reconstruction.

Main observations:

- Chamfer distance alone can overfit to noisy points.
- Strong smoothness creates a clean surface but removes important object details.
- A balanced combination of Chamfer, edge, and Laplacian losses produces the most visually plausible reconstruction.

The best result was achieved by Experiment C, which used balanced regularization.

## Key Findings

- Point cloud noise significantly affects mesh reconstruction quality.
- Regularization is necessary to avoid unstable and noisy surfaces.
- Too much regularization can oversmooth the mesh.
- The best reconstruction requires a balance between accuracy and smoothness.
- PyTorch3D provides useful tools for mesh deformation, point cloud sampling, and differentiable 3D optimization.

## Technologies Used

- Python
- PyTorch
- PyTorch3D
- Matplotlib
- NumPy
- 3D mesh processing
- Point cloud processing

## Possible Repository Structure

```text
surface-reconstruction-noisy-point-clouds/
│
├── README.md
├── notebooks/
│   └── surface_reconstruction.ipynb
│
├── data/
│   └── sofa_model.obj
│
├── outputs/
│   ├── point_cloud.png
│   ├── reconstruction_results.png
│   └── loss_curve.png
│
└── requirements.txt
```

## How to Run

1. Clone the repository:

```bash
git clone https://github.com/your-username/surface-reconstruction-noisy-point-clouds.git
cd surface-reconstruction-noisy-point-clouds
```

2. Install dependencies:

```bash
pip install -r requirements.txt
```

3. Run the notebook:

```bash
jupyter notebook notebooks/surface_reconstruction.ipynb
```

## Conclusion

This project demonstrates a deformable mesh approach for reconstructing a surface from a noisy point cloud. The results show that Chamfer distance alone is not enough for stable reconstruction. Strong smoothing improves stability but may remove important geometry. The best visual result is achieved when Chamfer distance, edge regularization, and Laplacian smoothing are combined in a balanced way.

The project confirms that regularization is essential for producing smooth and visually meaningful 3D reconstructions from noisy data.

## Author

Madiyar Bolatov  
AAI-2501M  
Astana IT University
