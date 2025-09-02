**For the Custom Res-SE Net scripts, see this repository [Custom Res-SE Net](https://github.com/nedatghd/Lung-Cancer-Diagnosis-from-2D-18F---PET-CT-Images/tree/main).**

Custom Res-SE Net is a custom-designed convolutional neural network for image classification, featuring a blend of Residual Blocks with Squeeze-and-Excitation (SE) attention and standard convolutional blocks. The architecture alternates between these two block types and culminates in a fully connected classifier.
<img width="895" height="462" alt="image" src="https://github.com/user-attachments/assets/4ac8c86c-759e-45a5-b3f4-710169742189" />
<img width="833" height="544" alt="image" src="https://github.com/user-attachments/assets/fff5322f-0d6e-440c-afe0-7bc221a0b2c9" />

 Schematic illustration of two primary building blocks: a) Res-SE Block, which integrates residual connections with channel-wise attention (Squeeze-and-Excitation), b) Norm. Conv Block consists of standard convolutional operations without skip connections 


________________________________________
🧱 Architecture Summary

Layer	Block Type	In Channels	Out Channels	Stride	SE Attention	MaxPooling	Dropout

1	ResidualSEBlock	3	16	2	✅	2×2	0.3

2	NormalConvBlock	16	32	1	❌	2×2	0.3

3	ResidualSEBlock	32	64	1	✅	2×2	0.3

4	NormalConvBlock	64	128	1	❌	2×2	0.4

5	ResidualSEBlock	128	256	1	✅	2×2	0.4

6	NormalConvBlock	256	512	1	❌	2×2	0.4

7	GlobalAvgPool	—	—	—	—	—	—

8	Fully Connected	512	256 → N/A	—	—	—	0.6
________________________________________
🧠 Block Definitions

1. ResidualSEBlock
   
•	A residual convolutional block with Squeeze-and-Excitation attention.

•	Structure:

o	Two 3×3 convolutions with BatchNorm and LeakyReLU (except last activation).

o	SE attention via global average pooling and channel-wise gating.

o	Identity or 1×1 convolution-based skip connection if shape mismatch.

o	Final output passed through LeakyReLU.

2. NormalConvBlock
   
•	A basic convolutional block without residual connection.

•	Structure:

o	Two 3×3 convolutions, both followed by BatchNorm and LeakyReLU.
________________________________________
🧮 Classifier Head

After all convolutional layers:

•	AdaptiveAvgPool2d(1) compresses spatial dimensions to 1×1.

•	Output is flattened to shape [batch_size, 512].

•	Fully connected layers:

o	Linear(512 → 256) → LeakyReLU → Dropout(0.6)

o	Linear(256 → num_classes)
________________________________________
🔍 Key Features

•	Squeeze-and-Excitation (SE): Channel-wise attention enhances feature representation.

•	Adaptive Pooling: Makes the model flexible to input image size (post-convolution).

•	LeakyReLU Activations: Avoids dying ReLU problem, improving feature flow.

•	Heavy Regularization:

o	Dropout2D after each layer (0.3–0.4)

o	Dropout(0.6) before final classification
________________________________________
📄 Data Preparation and Loading Pipeline

🔧 Hyperparameter Setup

The script defines key hyperparameters that control training dynamics and regularization:

•	Epochs: 200 — the number of times the model will iterate through the training data.

•	Batch Size: 64 — determines the number of samples processed before the model updates.

•	Learning Rate: 0.001 — controls the step size in the weight update.

•	Weight Decay: 1e-4 — a form of L2 regularization to reduce overfitting.

•	Early Stopping Patience: 20 — training halts if no improvement is seen over this many epochs.
________________________________________
🏋️ Training Transformations (Data Augmentation)

A robust set of augmentations is applied to artificially expand the dataset and improve generalization:

•	Resize((245, 457)): Standardizes input image dimensions.

•	RandomHorizontalFlip(p=0.5): Horizontally flips images with 50% probability.

•	RandomVerticalFlip(p=0.3): Vertically flips images with 30% probability.

•	RandomRotation(25): Rotates images randomly within ±25 degrees.

•	ColorJitter(...): Randomly changes brightness, contrast, and saturation.

•	RandomAffine(...): Applies affine transformation with shearing and scaling.

•	ToTensor(): Converts images into PyTorch tensors.

🔍 Testing/Validation Transformations

A minimal set of transformations is applied to ensure consistency and performance evaluation:

•	Resize((245, 457)): Matches the training input size.

•	ToTensor(): Converts images to tensor format.
