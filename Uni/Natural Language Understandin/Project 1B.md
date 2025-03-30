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

