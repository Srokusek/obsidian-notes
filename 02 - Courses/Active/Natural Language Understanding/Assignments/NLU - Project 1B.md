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
![[NIPS-2016-a-theoretically-grounded-application-of-dropout-in-recurrent-neural-networks-Paper.pdf#page=2&rect=111,571,498,725|NIPS-2016-a-theoretically-grounded-application-of-dropout-in-recurrent-neural-networks-Paper, p.2]]

The goal is to rectify the behavior of classical dropout, where a new dropout mask is created upon every pass, at every step of the word sequence. Variational dropout should then aim to keep the same mask across each step in the sequence. 

 - at each iteration, pass a binary mask
	 - should probably be different for each f the layers
	 - need to make sure the dimension is correct
	 - include the mask as one f the parameters for the forward pass
### Non-monotonically Triggered AvSGD

![[REGULARIZING AND OPTIMIZING LSTM LANGUAGE MODELS.pdf#page=4&rect=102,497,515,714|REGULARIZING AND OPTIMIZING LSTM LANGUAGE MODELS, p.4]]

Average stochastic gradient descent

 - SGD retains the same stochasticity over the learning process, learning rate could be slowly decreasing
	 
 - AvSGD slowly decreases the stochasticity by giving more and more attention to the "average" descent direction
 - Non-monotonically activated AvSGD does not decrease the stochasticity monotonically, instead it checks for some conditions (for example for lack of improvement in validation) to progress the T parameter which pushes it closer to averaging
 Using this technique helps alleviate the need to search for the right monotonic shift towards averaging. 