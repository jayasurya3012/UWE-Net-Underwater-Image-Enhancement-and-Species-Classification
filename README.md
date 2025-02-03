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
      ![image](https://github.com/user-attachments/assets/cd506fde-a6b0-4a5f-9643-4b072422c246)

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
regions. Given an Intermediate Feature map 'F', CBAM sequentially infers a 1D channel attention
map Mc, and a 2D spatial attention map Ms, then it can be summarized as, 
  ![image](https://github.com/user-attachments/assets/5b26567a-be34-4510-8405-0279f77ff684)

We present the composite fusion model, a novel architecture for classifying underwater 
species that integrates the properties of two pre-trained models: InceptionV3 and 
EfficientNetB5. Through the use of epoch-wise monitoring, we create ImproveCall, which 
effectively trains our architecture. This section will first discuss our recommended 
architectural design procedure, then our new training approach and any necessary data 
processing.
       ![image](https://github.com/user-attachments/assets/c2b80c60-f11d-4bd0-8473-ba763e934bc3)
To utilize the available VRAM and memory, we need the model to converge in the global 
minima without wasteful utilisation of memory and time. We had to develop a specific 
dynamic control training approach allowing us to interactively influence the training 
duration, observe real-time batch processing, and benefit from learning rate adjustments 
based on specified criteria. Here we use Epoch-wise Monitoring, i.e., for each epoch, 
training and validation metrics are obtained, and the training accuracy, validation accuracy, 
training loss, and validation loss are monitored. The learning rate is reduced by a specified 
factor when necessary and based on either training accuracy or validation loss, depending on 
the provided threshold value. The training is halted early if the learning rate is adjusted 
several times without improvement and the model's weights are set to the best-performing  
weights. The algorithm for this method can be described as:

Algorithm : ImproveCall 
Input: Threshold, Factor, Patience, and (Learning_rate : lr, accuracy : acc), val_accuracy : 
v_acc, loss : loss, val_loss : v_loss)) known to function at each epoch by the model. 
Output: Production of best weights for the model 
if acc < threshold: 
      monitor = 'accuracy' 
if epoch == 0: 
      pimprov = 0.0 
else: 
    if(self.highest_tracc == 0): 
          self.highest_tracc = 0.001 
                 pimprov = (acc - highest_tracc) * 100 / highest_tracc 
 
         if acc > highest_tracc: 
                 highest_tracc = acc 
                 best_weights = model.get_weights() 
             count = 0 
   stop_count = 0 
                 if v_loss < lowest_vloss: 
                         lowest_vloss = v_loss 
                 best_epoch = epoch + 1 
         else: 
                 if count >= patience - 1: 
                         lr = lr * self.factor 
                         tf.keras.backend.set_value(model.optimizer.lr, lr) 
                         count = 0 
                         stop_count = stop_count + 1 
                         if v_loss < lowest_vloss: 
                                 lowest_vloss = v_loss 
                 else: 
                         count = count + 1 
else: 
        monitor = 'val_loss' 
        if epoch == 0: 
                pimprov = 0.0 
        else: 
                pimprov = (lowest_vloss - v_loss) * 100 / lowest_vloss 
 
        if v_loss < lowest_vloss: 
                lowest_vloss = v_loss 
                best_weights = model.get_weights() 
                count = 0 
                stop_count = 0 
                best_epoch = epoch + 1 
        else: 
                if count >= patience - 1: 
                        lr = lr * factor 
                        stop_count = stop_count + 1 
                        count = 0 
                        tf.keras.backend.set_value(model.optimizer.lr, lr) 
                else: 
                        count = count + 1 
                if acc > highest_tracc: 
                        highest_tracc = acc 
 
if stop_count > stop_patience - 1: 
        model.stop_training = True 
else: 
        if ask_epoch != None and ask_permission != 0: 
                if epoch + 1 >= ask_epoch: 
                        ans = input(' ') 
                        if ans == 'H' or ans == 'h': 
                                 model.stop_training = True 
                else: 
                         try: 
                                 ans = int(ans) 
                                 ask_epoch += ans

