>[!wiki] Related papers
>[[Transformers are Graph Neural Networks.pdf]](Blog post)

Chaitanya Joshi
Published in 2020

### Representation learning for NLP

All NNs have some sort of representation mapping onto an embedding space

For NLP: usually RNN
 - parse one word at a time
 - retain context collected from previous words
 - Recently replaced by [[Transformers]]

RNNs x Transformers
 - Transformers no longer use recurrence
 - Replaced by [[Attention]] 
	 - Essentially gives the importance of other words as context to a given word
 - The parsing is no longer necessarily one word at a time

>[!note] Visualization
>![[Transformers are Graph Neural Networks.pdf#page=1&rect=109,222,482,369|Transformers are Graph Neural Networks, p.1]]

### Breaking down the transformer

Given features of a word $h_i^l$ and all other words from given sentence $w_j^l$ :
1. Compute the attention weights $w_{ij}$ by calculating the dot product between $h_i^l$ and $h_j^l$ And taking the softmax over all $j$ 
			$w_ij=softmax_j(Q^llh_i^l.K^lh_j^l)$ 



