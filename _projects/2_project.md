---
layout: page
title: Referring Image Segmentation via Text-to-Image Diffusion Models
description: Referring Image Segmentation via Text-to-Image Diffusion Models
img: assets/img/3.jpg
importance: 2
category: work
giscus_comments: true
---

# Project Page

## Introduction

![VPD Architecture](![Segmentation Mask Comparison](https://github.com/melvinsevi/melvinsevi.github.io/blob/master/assets/img/VPD.png)
)

In this project, we aim to improve the performance of the Visual Phrase Grounding (VPD) model proposed by Zha and Rao (Year), focusing particularly on enhancing attention maps for better segmentation accuracy. The VPD model combines vision and language to localize objects referred to by natural language expressions within an image. By refining the attention mechanism of the model, we aim to achieve more accurate and robust object localization.

## Results

### Segmentation Mask Comparison

![Segmentation Mask Comparison](no_mistake/input_image739.png)
*Blurry click down about an inch from top right corner*

![Segmentation Mask Comparison](no_mistake/input_image745.png)
*Adult to left of kid*

### Attention Map Analysis

![Attention Map Analysis](no_mistake/input_image87.png)
*The fully visible zebra*

### Segmentation Mask Evaluation

![Segmentation Mask Evaluation](no_mistake/input_image87.png)
*The fully visible zebra*

## Model Training

### Training Setup and Results

To assess the computational and qualitative efficiency of retraining the entire SD, which gather near 900M parameters and is initially trained on a single high-end NVIDIA A100 GPU (3$/hour on G-Cloud), we aimed to unlock its full potential while making it feasible to train on commonly available GPU on G-Cloud. Our strategy involved significantly reducing the number of trainable parameters. Concretely, we froze the SD U-Net and disabled the text adapter, thereby shrinking the parameter count from 899M to 39M. Despite this decrease, training continued to be computationally intensive even on top Nvidia GPUs like the T4, P100, or L4 also due to insufficient VRAM memory (unfeasible to obtain multiple GPU machines promptly). 

To enhance training optimization, we chose a more manageable subset consisting of 3,000 referring expressions from the training set of RefCOCO with 1000 expressions in the validation set. The split is done by taking always the first images in the original split. By employing this split with a diminished batch size of 4 (32 in the paper), we completed training over two epochs using an identical initial learning rate of 0.005 and weight decay value of 0.01 mentioned in the original code. We also used Adam optimizer and cross entropy loss for training. Additionally, we attempted a complete dataset training run with a batch size of 4 (totaling 14 hours); however, the outcomes were poor (see Fig. \ref{tab:mytable3}), likely attributed to the small batch size and quantity of epochs. 

Experimental evidence suggests that enhancing the model through attention map is beneficial. So our real goal here is to provide the best attention maps to the "classifier". The problem is that implementing direct cross-attention between CLIP text features and the U-Net feature maps has shown less good performance. The removal of the adapter seems to only sacrifices flexibility without substantial improvement. Using only CLIP is clearly not enough to directly get good segmentation in the attention maps. Indeed these attention maps in the latent space gets better during the generation process of an image by SD (Zhang et al., 2023) but directly trying to get the attention maps from the cross attention between the caption tokens and the feature maps from the U-Net does not give the same results (see Tab. 2). We propose new ideas to address this problem at the end of the paper.

### Model Training and Use of Different Noise Scale

The (VPD) model demonstrates remarkable performance, achieving 73.56 oIoU on the validation dataset of RefCoCO. However, when freezing SD parameters and removing the Adapter during training, the results are noticeably inferior. In pursuit of improved robustness, another approach explored varying levels of noise applied to the input images both through training and inference. To evaluate the impact of diverse noise scales, we compared attention maps generated across various timestamps during inference of the original VPD. Unfortunately, upon completing the training phase of the freezed SD VPD (F-VPD), the model's performance on the validation set remains disappointing (see Tab. 3), rendering attention map visualizations unnecessary. Interestingly, during inference, attention maps remain good (see Fig. 7) even under significant noise conditions up to a noise scale factor of 500. As a result, we hypothesize that either the latent representations of clean and noisy images retain considerable shared information, or the U-Net produces similar feature maps regardless of the imposed noise level. Ultimately, the objective involves obtaining optimal segmentations via enhanced attention map so we give insight in the last part on ideas to use different noise scale to obtain better attention maps.

## Further research approach:

1. Utilizing High-Resolution Feature Maps: Our current implementation uses 32x32 and 16x16 resolution attention maps fed into the classifier. However, increasing the resolution to 64x64 may yield improved results by providing more detailed spatial information about objects within the image. 
2. Fine-Tuning Using Low Rank Adaptation Matrices (Hu et al., 2023): To avoid re-training the entire model, fine-tuning specific components can result in improved performance while reducing the required computation. By adjusting the different Q, K, and V weight matrices responsible for cross-attention between textual features and U-Net feature maps, lower rank matrices can be added to maintain information from pre-trained models without training numerous new parameters. 
3. Obtaining Higher Resolution Attention Maps via Upsampling: Inspired by the (DAAM) paper (Tang et al., 2023), upsampling attention maps could deliver finer attention details and produce higher resolution output suitable for feeding into the subsequent classification module.
4. Generative Conditioned Denoising through Referring Captions: Adopting a generative approach to obtain better segmentation attention maps may be beneficial. Specifically, adding noise to the image and guiding its denoising based on the provided referring captions could potentially refine attention mechanisms and offer enhanced accuracy.

## Conclusion

In this study, we aimed to improve the VPD model by Zha and Rao, focusing on enhancing attention maps for better performance. We initially attempted to simplify the model and fully exploit self-distillation, discovering the necessity of using an adapter for effective text embedding refinement. We also explored the impact of different noise scales as inputs, finding the VPD's robustness to higher noise scales and sensitivity to attention map quality. Unfortunately, due to computational limitations, we couldn't achieve conclusive results. Nonetheless, our findings provide valuable insights, suggesting promising directions for future research to refine the VPD model.

**References**

- Hu, Y., et al. (2023). *Title of Hu et al.'s Paper*. Journal Name.
- Tang, Z., et al. (2023). *Title of Tang et al.'s Paper*. Journal Name.
- Zhang, X., et al. (2023). *Title of Zhang et al.'s Paper*. Journal Name.

