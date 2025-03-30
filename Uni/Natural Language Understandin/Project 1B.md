Using the paper [[REGULARIZING AND OPTIMIZING LSTM LANGUAGE MODELS.pdf]]


### topics for improvement
1. Weight tying
2. Variational dropout
3. Non-monotonically Triggered AvSGD

### Weight tying

![[REGULARIZING AND OPTIMIZING LSTM LANGUAGE MODELS.pdf#page=5&rect=106,248,510,306|REGULARIZING AND OPTIMIZING LSTM LANGUAGE MODELS, p.5]]

Weight tying makes the network share weights between the embedding and softmax layer. Sharing the weights effectively makes the number of parameters learned smaller. 

> [!PDF|255, 208, 0] [[REGULARIZING AND OPTIMIZING LSTM LANGUAGE MODELS.pdf#page=5&annotation=523R|REGULARIZING AND OPTIMIZING LSTM LANGUAGE MODELS, p.5]]
> > prevents the model from having to learn a one-to-one correspondence between the input and output


### Variational dropout

![[REGULARIZING AND OPTIMIZING LSTM LANGUAGE MODELS.pdf#page=5&rect=104,450,511,605|REGULARIZING AND OPTIMIZING LSTM LANGUAGE MODELS, p.5]]

The goal is to rectify the behavior of classical dropout, where a new dropout mask is created upon every pass. Variational dropout instead creates one binary mask when called. This results in creating one binary mask for every "minibatch". In a way, Variational Dropout is a balance between normal dropout and no dropout. It allows the network to learn a more stable architecture, but also be robust enough since the mask does change every minibatch.

 - at each iteration, pass a binary mask
	 - should probably be different for each f the layers
	 - need to make sure the dimension is correct
	 - include the mask as one f the parameters for the forward pass