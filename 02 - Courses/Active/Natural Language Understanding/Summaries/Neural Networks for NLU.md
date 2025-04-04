### Regularization

Various techniques to prevent overfitting

Examples:
- L1 and L2 regularization in LogReg
- Dropout

### Recurrent Neural Networks

>[!definition] Recurrent Neural Network
>Any neural network which includes a cycle that loops back to itself, making states dependent on previous passes
>![[NLU-NN.pdf#page=11&rect=64,61,754,479|NLU-NN, p.11|200]]

 RNNs are meant to provide a way of carrying meaning as we parse token-by-token

Usually only operates left to right, but can also use **bidirectional** networks
- Looks at “left” context (as per usual)
- Looks also at “right” context

>[!definition] Bidirectional RNN
>RNN with an additional $U’$ weights for looking both at left and right context

### Transformers

Motivation: RNNs are not able to keep track of context in long sequences
=> Transformers solve this issue

Transformers instead based n attention, not recurrent loops
First introduced for machine translation

>[!note] Transformer architecture
>![[NLU-NN.pdf#page=15&rect=40,99,767,486|NLU-NN, p.15]]

Parts of transformers:
 
### Encoder - input embeddings

- tokenization: byte pair encoding = sub-token
- Token embeddings: learned by training
- Positiona embeddings: transformers do not keep track of sequence by default, need to make use of positional embeddings


#### Tokenization: Byte-Pair Encoding

1. Initialization
	1. Tokenize corpus at character level
	2. Decide vocabulary size S
2. Repeat until vocab size equal to S
	1. Find most frequent pair of symbols $X$ and $Y$ 
	2. Create new symbol $XY$ and add it to the vocabulary
	3. Substitute all instances of $X,Y$ by $XY$  
	
>[!note]- Example
>![[NLU-NN.pdf#page=21&rect=65,195,769,469|NLU-NN, p.21]]

=> no longer have any OOV 

#### Self-Attention Mechanism
Idea: take input and pass to 3 linear layers, output should tell us which token we should be paying attention to

![[NLU-NN.pdf#page=24&rect=78,169,687,474|NLU-NN, p.24]]

### Different transformer models
1. BERT (Bidirectional Encoder Representation from Transformer)
2. GPT models
	only use decoder, not possible for bidirectionality
3. T5
	Encoder-decoder

### BERT
Trained on masked language modeling

>[!note] Masked language modeling
>Mask words inside text, use train model on predicting masked words

![[NLU-NN.pdf#page=26&rect=330,34,771,469|NLU-NN, p.26|400]]


### How to use on natural language understanding

>[!note]- Spoken dialogue system of NLU
>![[NLU-NN.pdf#page=28&rect=211,36,641,461|NLU-NN, p.28]]

#### Semantic Frame
- require domain ontology
	- Set of **intents**
	- Set of associated **slots** (variables associated with give intent)
![[NLU-NN.pdf#page=29&rect=107,152,707,288|NLU-NN, p.29|300]]

#### NLU task
main tasks:
1. Domain classification
	What is the domain of the user request?
2. Intent classification
	What is the request intent?
3. Slot filling
	What are the variables related to intent?

>[!note]- Benchmark datasets
>ATIS - Airline Travel Information System
>SNIPS

#### Intent classification and slot filling

Best learned together
	Knowing the intent helps with locating slots
	Also Vise-versa
=> Multi-task Learning

>[!note]- Multi-task Learning in transformers
>![[NLU-NN.pdf#page=37&rect=88,126,699,425|NLU-NN, p.37]]

