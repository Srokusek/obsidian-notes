[[ABNS - Assignment2 Instructions.pdf]]

## Part 1: Code Understanding

![[ABNS - Assignment2 Instructions.pdf#page=1&rect=89,383,364,433|ABNS - Assignment2 Instructions, p.1|400]]

The fc1 layer is the first layer to come after the convolutional layers. It is a fully connected layer that uses as input the flattened features coming from the convolutional layers. In some sense, the convolutional layers act as feature extractors from the image (they are much more reliable for this based on their shift invariance and ability to look at local structures). The fc1 is then the fully connected layer which takes the features extracted and maps them onto the hidden dimension. It is the hidden layer which should help with classification in the ultimate layer. 

![[ABNS - Assignment2 Instructions.pdf#page=1&rect=87,329,535,376|ABNS - Assignment2 Instructions, p.1]]
One of the goals of the paper by Lake et al. was to show that ANNs have a similar notion of typicality within classification categories as has been found in experiments with humans. 

The logits give a value for the activation of the weights related to a given category upon feeding the network an image from that category. The value is not normalized and relates to the **Raw typicality**. This measure can quite hard to determine in experiments by humans (how to give a number to the typicality of an arbitrary image?) Instead, it is much easier to do experiments by asking comparison style question (which of these images is more typical of class x?)

The softmax output gives the normalized probabilities between all the outputs. The idea is that if an image is typical of one category, the other probabilities should be low. In this sense, the softmax represents the **Contrast typicality**, by not only looking at the individual activations, but also normalizing them with regards to the other categories. It is also much easier to do experiments with humans where comparing typicality is the task.

![[ABNS - Assignment2 Instructions.pdf#page=1&rect=87,205,540,320|ABNS - Assignment2 Instructions, p.1]]
They are both aimed at giving us an idea about the distance within the embedding space from the average of a given category. Similarly as in clustering, the samples very close to the mean of a given category are seen as "strong classifications" and therefore more typical of that category. 

The **Euclidean distance** measures the norm of the vector between the category average and the sample. The measure is very simple to understand, but one of the downsides is that it is relative to the of the embeddings vectors. The network should learn the projection into the embeddings space in such a way that enables best discrimination on the classification task, it is not always encouraged to make the embedding space any particular size. The sizes could also generally vary between categories, which would bring further trouble when making comparisons.

The **cosine similarity** fixes some of these issues by comparing the angle between the mean embedding vector and the vector from a given sample. This eliminates the problem of the different sizes of vectors. 

As to when we want to chose on over the other, there is no one correct answer. It will depend heavily on the size of the data and the complexity/architecture of the network. If the embedding space is expressive enough (high dimensionality), the cosine similarity should be able should give good (similar) recommendations (it is able to discriminate well even without using the distances). If the embedding space is more low-dimensional, the additional information about position we get from the Euclidean we get might be beneficial (imagine classification task in a 2d space). In general, for recommendation systems cosine similarity should be able to do the task well, but in case we want to keep information about the actual size of the distance we can choose to use the Euclidean distance (or the dot product, Manhattan distance etc. for faster computations)

## Part 2: Analysis and Comparison

![[ABNS - Assignment2 Instructions.pdf#page=1&rect=87,99,526,153|ABNS - Assignment2 Instructions, p.1]]

When choosing the the category truck, I received the following spearman correlation matrix:
![[Pasted image 20250409140017.png|400]]

Significant correlation (>0.7) was found for logit-probability ($0.746$) and logit-cosine ($0.799$). The weakest correlation is that of logit-probability at $-0.01$. The correlation between logit and probability should be fairly obvious, as logits are a step before the softmax layer converts them into probabilities. Since probabilities are essentially normalized logit values, high correlation is to be expected. 
The high logit-cosine correlation hints that the model uses the relative angle of the embeddings to split the space into classification. 
The (pretty much) zero logit-Euclidean correlation hints that the model internally does not use the Euclidean distance to split the embedding space. Another reason might be that the norm of the embedding vectors (which is not ignored in the Euclidean case) is highly random and thus negates any correlation that might be present.

![[ABNS - Assignment2 Instructions.pdf#page=2&rect=86,641,526,721|ABNS - Assignment2 Instructions, p.2]]

![[Pasted image 20250409141635.png|300]] ![[Pasted image 20250409141809.png|300]]
![[Pasted image 20250409141845.png|300]] ![[Pasted image 20250409141901.png| 300]]
Comparing the images with regards to the previous exercise, we get some similar outcomes. The relatively high pair-wise correlation between logit, softmax probability and cosine similarity can be seen by the fact that they all share some of the top5 images, furthermore they share 4 out of 5 of the bottom 5 images. This shows that while the images at the centers are slightly varied, the images on the edges of the category are similar for all three metrics.
The Euclidean distance has some very different results, where only one of the top5 images is shared (with cosine similarity) and the bottom5 images actually has what softmax and logit determined to be most typical.

![[ABNS - Assignment2 Instructions.pdf#page=2&rect=85,546,542,634|ABNS - Assignment2 Instructions, p.2]]

The weight vectors give insight into which features are considered for classification of each of the classes. The average embedding show what are the average activations when doing a forward pass of images within a given category (in the test set in this case). 
If the cosine similarity is high, we expect that the features used for classifications are present within the image. For example the high similarity ($0.390$) between ship and airplane shows that similar features are used as indicators for selecting ship or airplane class. 
A zero similarity would should that the embeddings and weights would be mostly orthogonal or irrelevant on the decision made. This is the example for the frog-dog case.
A negative similarity means that the detected features which lead to an increased activation in one category lead to a decreased activation in the other. An example being the horse-automobile.