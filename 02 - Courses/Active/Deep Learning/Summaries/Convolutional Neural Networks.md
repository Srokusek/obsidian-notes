
A special case of the FFN, with local connectivity and shared weight parameters. This makes the network shift-invariant. 

First ideas, stacking multiple layers of feature extractors on local patterns (low level) which are then fed into the high-level layers to extract global patterns.

#### Layers

![[Lec 9 -CNN.pdf#page=24&rect=37,61,698,137|Lec 9 -CNN, p.24]]

### More on Convolutional Layer

 - consists of a set of filters
 - Receptive field = covered local portion of input
 - The layer learns the filter

Hyperparamters:
 - Size
 - Stride
 - Padding
	 - Zero padding
	 - Other options
 - Output size = $(N-K)/S+1$ 

Dimension of conv layers is a tensor with volume
![[Lec 9 -CNN.pdf#page=34&rect=29,32,699,242|Lec 9 -CNN, p.34]]

### CNN summary

 - goal is to have shift-invariant network
 - Based on the convolution detection
	 - In this case the convolution kernel/filter is learned to help with prediction
 - Padding helps preserve input size
 - Stride determines how many convolutions we perform
 - Convolution layers operate over volumes (different filters result in different outputs)
 - Convolutional layers include multiple kernels
 - The size of the input does not matter, we are learning the parameters based on the size of the kernel

Note: CNNs are shift-invariant only to some degree 

#### Development of CNNs

![[Lec 9 -CNN.pdf#page=37&rect=224,44,697,347|Lec 9 -CNN, p.37]]

#### LeNet

Considered to be the start of CNNs
Used for digits recognition

Two convolution-pooling layer pairs

![[Lec 9 -CNN.pdf#page=38&rect=218,101,708,310|Lec 9 -CNN, p.38]]

#### AlexNet

Similar to LeNet, but bigger

Used GPUs for calculation
Dataset: Imagenet 224x224 images with 1000 labels

Uses a 4096 dimensional feature space 
	This became the rule of thumb for CNNs
AlexNet started to extract general features that were useful of multiple tasks

>[!note]- Visualization of the representation in feature space
>https://cs.stanford.edu/people/karpathy/cnnembed/


![[Lec 9 -CNN.pdf#page=41&rect=36,124,428,275|Lec 9 -CNN, p.41]]

#### Universal representations

One of the most significant contributions was the change from feature engineering to feature extraction.

In terms of images, CNNs were very good at extracting features, which ended up being useful for different applications

>[!note] Fine Grained Recognition
>Some results showed that accuracy was usually improved when we added some extra architecture for special purpose tasks
>![[Lec 9 -CNN.pdf#page=52&rect=212,166,715,303|Lec 9 -CNN, p.52]]

#### Deconvolutional Network

The need to understand the different activations inside the network became increasingly of interest

Goal: interpret activity in intermediate layers
Idea: Map the activations back to the pixel space

Deconv network: CNN ran in reverse

 >[!note]- Architecture:
 >![[Lec 9 -CNN.pdf#page=57&rect=346,79,685,385|Lec 9 -CNN, p.57]]
 
 >[!note]- Result
 >![[Lec 9 -CNN.pdf#page=58&rect=200,106,711,307|Lec 9 -CNN, p.58]]
 
 Other approaches:
  - Occlusion experiments 
	  - Simulating occlusion over different portions of the image
	  - Visualize the different activations
>[!note]- Visualization
>![[Lec 9 -CNN.pdf#page=60&rect=226,47,689,300|Lec 9 -CNN, p.60]]


>[!cons] Possibility of adversarial attacks
>CNNs showed behavior were constructed perturbations to the data massively affected the output
>![[Lec 9 -CNN.pdf#page=62&rect=3,73,394,233|Lec 9 -CNN, p.62]]


>[!definition] Class Model Visualization
>Numerical generation of a class
>Uses maximization to find maximal activation for a given class
>![[Lec 9 -CNN.pdf#page=64&rect=290,16,612,248|Lec 9 -CNN, p.64]]
>>[!corollary] Bonus
>>Attempts of class visualization of multimodal NNs

#### Gradcam

>[!definition]- CAM
>Convolution Activation Map

Using global average pooling to which reasons contribute to class activation

>[!cons] Limitations
>CAM requires the network to be fully Convolutional

=> Use the gradient of CAM: GradCAM

Take gradient wrt to a category and activation

>[!note]- Visualization
>![[Lec 9 -CNN.pdf#page=66&rect=238,30,684,205|Lec 9 -CNN, p.66]]

>[!note]- Architecture
>![[Lec 9 -CNN.pdf#page=67&rect=232,134,669,347|Lec 9 -CNN, p.67]]

#### VGG

Same structure as AlexNet, but much deeper
>[!note] Architecture comparison
>![[Lec 9 -CNN.pdf#page=69&rect=226,55,694,295|Lec 9 -CNN, p.69]]

Showed better result, but the number of parameters was really huge for the time

#### GoogleNet

Opposite approach to VGG - reduction of parameters when compared to AlexNet

Removed fully connected layer

Includes parallel paths with different receptive fields
	Combination of depth and width

Includes 1x1 convolution
	Idea is to limit the number of channels, reduces number of parameters
	Provides a **significant** reduction in the number of parameters

>[!faq] what is a 1x1 convolution
> Need image from new pdf

Introduction of batch norm

#### Rethinking Inception
 - a google research paper
 - Focus on the different designs of CNNs

Showed benefits of using smaller filters (3x3)
=> Inception V2 - V3
 - Include 1x1 conv layers to reduce number of parameters
 - Balance between width and depth
 - Avoid extreme compression layers 

#### ResNet
- Residual Network
Motivated by a plot showing a shallow network outperforming a deeper network
![[Lec 9 -CNN.pdf#page=78&rect=465,250,590,294|Lec 9 -CNN, p.78]]

Showed that using more layers does not necessarily improve the outcome 
=> include identity bypass
>[!note]- Identity Bypass
>Allows the network to decide to skip a layer
>Introduced in ResNet and lead to significant improvements
>![[Lec 9 -CNN.pdf#page=80&rect=278,280,498,389|Lec 9 -CNN, p.80]]

##### Further implementations
Ideas:
 - change the size of the bypass
	 - How many layers should the network “skip”
- Change which layers are skipped
	- It does not have to apply only to convolutional layers

Insights:
 - ResNet works similarly to an ensemble method
	 - Chooses which of the layers should be used
- Loss landscape becomes much smoother
	- Easier to optimize over

##### Other ResNet-like networks
 - Wide ResNet
	 - Increase the width of ResNet
	 - MultiResNet
	 - PolyNet
	 - Inception ResNet
None replaced classical ResNet until DenseNet

##### DenseNet

Idea: every layer is connected to all other layers

Interconnectedness encouraged reusing features
>[!note]- Architecure
>![[Lec 9 -CNN.pdf#page=93&rect=447,153,702,323|Lec 9 -CNN, p.93]]

#### SENet
- Squeeze and Excitation network

- Squeeze 
	- Global information embedding
	- Computes average value of every channel
- Excitation
	- Adaptive recalibration
	- Compute weight for every channel via a sigmoid activation function
=> adaptively recalibrates channel-wise features

Indirectly lead to transition towards transformers (in some applications)














![[Lec 9 -CNN.pdf]]