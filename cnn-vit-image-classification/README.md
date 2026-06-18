# Comparative Analysis of CNN and Vision Transformer Architectures for Image Classification

This repository contains a comparative analysis of **Convolutional Neural Networks (CNNs)** and **Vision Transformers (ViTs)** for image classification.

The project studies both architecture families from theoretical and practical perspectives and explains which approach is more suitable for image classification under constrained computational resources and high accuracy requirements.

## Author

**Madiyar Bolatov**  
AAI-2501M  
Astana IT University

## Project Overview

Image classification is a fundamental computer vision task where an input image is assigned to one of several predefined classes.

This project compares two major approaches used for image classification:

- **Convolutional Neural Networks (CNNs)**
- **Vision Transformers (ViTs)**

CNNs have traditionally been the standard architecture for image-based tasks because they efficiently learn local visual patterns. Vision Transformers, on the other hand, adapt the Transformer architecture to images and use self-attention to model long-range relationships between image regions.

The main goal of the project is to understand the strengths, limitations, and practical use cases of both architectures.

## Objectives

The objectives of this project are:

- to explain how CNNs process image data;
- to explain how Vision Transformers process image data;
- to compare CNNs and ViTs in terms of accuracy, data requirements, transfer learning, and computational efficiency;
- to identify which architecture is more practical under limited computational resources;
- to summarize the trade-offs between local feature extraction and global self-attention.

## Background

### Convolutional Neural Networks

CNNs are designed for spatially structured data such as images. They use convolutional filters that move across the image and detect local visual patterns.

Important CNN concepts include:

- **Convolution** — extracts local features such as edges, corners, and textures;
- **Pooling** — reduces spatial dimensions and improves robustness to small shifts;
- **Hierarchical feature learning** — builds complex object-level features from simple low-level patterns.

Common CNN architectures include:

- ResNet
- EfficientNet

CNNs are efficient because they use strong visual inductive biases such as locality and translation equivariance.

### Vision Transformers

Vision Transformers adapt the Transformer architecture to computer vision.

Instead of using convolutional filters, a ViT divides an image into fixed-size patches. These patches are converted into token embeddings and processed through Transformer blocks.

Important ViT concepts include:

- **Patch embedding** — splits the image into patches and converts them into vectors;
- **Positional embedding** — preserves spatial information about patch locations;
- **Self-attention** — allows each patch to interact with all other patches;
- **Global context modeling** — captures long-range dependencies directly.

Common Vision Transformer architectures include:

- ViT
- DeiT
- Swin Transformer

ViTs are powerful because self-attention allows them to model global relationships across the image.

## CNN vs Vision Transformer

### How They Process Images

| Aspect | CNN | Vision Transformer |
|---|---|---|
| Input representation | Pixel grid | Sequence of image patches |
| Main operation | Convolution | Self-attention |
| Focus | Local patterns first | Global relationships |
| Spatial bias | Strong built-in bias | Requires positional embeddings |
| Feature learning | Hierarchical | Attention-based |
| Data efficiency | Usually better with smaller data | Usually needs more data or pretraining |

CNNs see an image as a continuous spatial structure and gradually build higher-level features from local regions.

ViTs treat the image as a sequence of visual tokens and use self-attention to connect information between distant regions.

## Transfer Learning and Fine-Tuning

Transfer learning is important for both CNNs and ViTs.

### CNN Transfer Learning

CNN transfer learning is usually stable because early convolutional layers learn general visual features such as:

- edges;
- gradients;
- textures;
- simple shapes.

These features can often be reused across different image classification tasks.

### ViT Transfer Learning

ViTs also benefit strongly from pretraining. However, they are usually more sensitive to:

- learning rate;
- augmentation;
- regularization;
- fine-tuning strategy;
- dataset size.

With strong pretraining, ViTs can achieve excellent performance, but they often require more careful tuning than CNNs.

## Data Requirements

When trained from scratch, Vision Transformers usually require more data than CNNs.

This happens because CNNs have stronger built-in assumptions about image structure. These assumptions help CNNs learn useful features even when the dataset is not very large.

ViTs are more flexible, but this flexibility means they need more data to learn robust visual representations.

In general:

```text
Small or medium dataset: CNN is usually more practical
Large dataset or strong pretraining: ViT can be very competitive
```

## Computational Efficiency

CNNs are often more practical when computational resources are limited. They are efficient because convolution is well optimized and naturally matches the grid structure of images.

ViTs can be computationally expensive because self-attention compares many image patches with each other. For high-resolution images, this can increase memory and compute requirements.

However, newer architectures such as Swin Transformer improve efficiency by applying attention within local windows instead of across the whole image at once.

## Practical Comparison

| Criterion | CNN | Vision Transformer |
|---|---|---|
| Works well with limited data | Strong | Weaker without pretraining |
| Computational efficiency | Usually better | Often more expensive |
| Fine-tuning stability | More stable | More sensitive |
| Global context modeling | Gradual | Direct |
| Local feature extraction | Strong | Less natural |
| Scalability with large data | Good | Very strong |
| Practical deployment | Easier | More demanding |

## Main Findings

The comparison shows that CNNs and ViTs have different strengths.

### CNN Strengths

CNNs are strong because they:

- efficiently capture local visual patterns;
- perform well with smaller datasets;
- are easier to fine-tune;
- require fewer computational resources;
- are practical for deployment.

### CNN Limitations

CNNs can be limited because:

- global context is captured gradually through deeper layers;
- long-range dependencies may be harder to model directly;
- performance may saturate compared with large pretrained transformer models.

### Vision Transformer Strengths

ViTs are strong because they:

- model long-range relationships directly;
- scale well with large datasets;
- benefit significantly from large-scale pretraining;
- can capture global image context effectively.

### Vision Transformer Limitations

ViTs can be limited because they:

- usually require more training data;
- need careful fine-tuning;
- can be computationally expensive;
- depend strongly on pretraining and regularization.

## Results

By training ResNet18, ViT-Tiny, DeiT-Tiny and EfficientNet-B0 on CIFAR-10 using pretrained models:

<p align="center">
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/main/cnn-vit-image-classification/figures/acc_graph.jpg"/>
</p>

<p align="center">
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/main/cnn-vit-image-classification/figures/acc_table.jpg"/>
</p>

<p align="center">
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/main/cnn-vit-image-classification/figures/resnet_results.jpg"/>
</p>

<p align="center">
  <img src="https://github.com/Mad03633/Computer-graphics-modelling_ai/blob/main/cnn-vit-image-classification/figures/efficientnet_results.jpg"/>
</p>

## Conclusion

The project concludes that both CNNs and Vision Transformers are effective for image classification, but they are suitable for different conditions.

For constrained computational resources and smaller datasets, CNNs are usually the more practical choice. They are efficient, stable, and naturally suited to image data.

Vision Transformers become more attractive when large-scale pretraining, enough data, and sufficient computational resources are available. Their self-attention mechanism allows them to capture global image relationships more directly than CNNs.

In summary:

```text
CNNs are generally better for efficiency and limited-data scenarios.
ViTs are more powerful when large data and strong pretraining are available.
```

## Technologies and Concepts

This project focuses on the following concepts:

- Image classification
- Convolutional Neural Networks
- Vision Transformers
- Transfer learning
- Fine-tuning
- Patch embeddings
- Positional embeddings
- Self-attention
- Computational efficiency
- Data requirements in deep learning


FOR MORE INFO, CHECK Report.pdf!!!