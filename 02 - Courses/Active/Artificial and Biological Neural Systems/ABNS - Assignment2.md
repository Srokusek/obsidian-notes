[[ABNS - Assignment2 Instructions.pdf]]

## Part1: Code Understanding

![[ABNS - Assignment2 Instructions.pdf#page=1&rect=89,383,364,433|ABNS - Assignment2 Instructions, p.1|400]]

The fc1 layer is the first layer to come after the convolutional layers. It is a fully connected layer that uses as input the flattened features coming from the convolutional layers. In some sense, the convolutional layers act as feature extractors from the image (they are much more reliable for this based on their shift invariance and ability to look at local structures). The fc1 is then the fully connected layer which takes the features extracted and maps them onto the hidden dimension. It is the hidden layer which should help with classification in the ultimate layer. 

![[ABNS - Assignment2 Instructions.pdf#page=1&rect=87,329,535,376|ABNS - Assignment2 Instructions, p.1]]
One of the goals of the paper by Lake et al. was to show that ANNs have a similar notion of typicality within classification categories as has been found in experiments with humans. 

The logits give a value for the activation of the weights related to a given category upon feeding the network an image from that category. The value is not normalized and relates to the **Raw typicality**. This measure can quite hard to determine in experiments by humans (how to give a number to the typicality of an arbitrary image?) Instead, it is much easier to do experiments by asking comparison style question (which of these images is more typical of class x?)

The softmax output gives the normalized probabilities between all the outputs. The idea is that if an image is typical of one category, the other probabilities should be low. In this sense, the softmax represents the **Contrast typicality**, by not only looking at the individual activations, but also normalizing them with regards to the other categories. It is also much easier to do experiments with humans where comparing typicality is the task.

![[ABNS - Assignment2 Instructions.pdf#page=1&rect=87,205,540,320|ABNS - Assignment2 Instructions, p.1]]
They are both aimed at giving us an idea about the distance within the embedding space from the average of a given category. Similarly as in clustering, the samples very close to the mean of a given category are seen as "strong classifications" and therefore more typical of that category. 

The Euclidean distance measures the norm of the vector between the category average and the sample. The measure is very simple to understand, but one of the downsides is that it is relative to the of the embeddings vectors. The network should learn the projection into the embeddings space in such a way that enables best discrimination on the classification task, it is not always encouraged to make the embedding space any particular size. The sizes could also generally vary between categories, which would bring further trouble when making comparisons.

The cosine similarity fixes some of these issues by comparing the angle between the mean embedding vector and the vector from a given sample. This eliminates the problem of the different sizes of vectors. 

As to when we want to chose on over the other, there is no one correct answer. It will depend heavily on the size of the data and the complexity/architecture of the network. If the embedding space is expressive enough (high dimensionality), the cosine similarity should be able should give good (similar) recommendations (it is able to discriminate well even without using the distances). If the embedding space is more low-dimensional, the additional information about position we get from the Euclidean we get might be beneficial (imagine classification task in a 2d space). In general, for recommendation systems cosine similarity should be able to do the task well, but in case we want to keep information about the actual size of the distance we can choose to use the Euclidean distance (or the dot product, Manhattan distance etc. for faster computations)

