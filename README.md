# UWE-Net for Underwater Image Enhancement and Composite Fusion model for Species Classification
We introduce UnderWaterEnhanceNetwork (UWE-Net), a novel underwater picture 
enhancement technique that uses a pre-trained architecture termed VGG16 as an encoder. In 
our method, we add a Convolutional Block Attention Module to the decoder's skip 
connections. This module records the image's spatial and channel-wise dependencies. We 
employ the Charbonnier loss as a measure for enhancing underwater images in order to get 
better results.

## VGG16 Blocks as Encoder Blocks: 
The encoder of the model employs a pre-trained 
VGG16 architecture with fixed weights. Attention mechanisms are applied to intermediate 
feature maps at various resolutions within the VGG16 encoder blocks. Each encoder block 
contains one convolutional layer and max pooling layer respectively. The decoder blocks 
include transposed convolutional layers and combine features from corresponding encoder 
blocks to recover spatial information lost during downsampling. This integration enables the 
model to effectively utilize the pre-trained weights for extracting high-level semantic 
features, thereby enhancing overall performance. 

To address the challenges in enhancing 
underwater images, including overlapping artifacts and low-light quality, we use a holistic 
attention mechanism. This mechanism combines the Channel Attention 
Module (CAM) and the Spatial Attention Module (SAM), enabling our model to capture 
both channel-wise and spatial dependencies. The CAM focuses on enhancing channel-wise 
feature representations within each feature map. It achieves this by computing attention 
weights based on global average pooling and global max pooling operations. These attention 
weights modulate the original features, allowing the model to emphasize informative 
channels while suppressing less relevant ones. 
Complementing the CAM, the SAM emphasizes spatial regions of importance within 
the feature maps. It employs global average pooling and global max pooling to obtain spatial 
attention weights. These weights are then combined through convolutional operations and 
applied to the original features, enabling the network to adaptively highlight crucial spatial 
regions. Given an Intermediate Feature map               
1D channel attention map                 
, 
CBAM sequentially infers a 
,and a 2D spatial attention map                
then it can be summarized as, 

![image](https://github.com/user-attachments/assets/5b26567a-be34-4510-8405-0279f77ff684)
