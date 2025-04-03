Slides: [[NLU-Sentence.pdf]]

Motivation: sentences are the basic blocks of human communication

### What is in a sentence
- Sequence of tokens
- Embeds the speakers intention
- Embeds the expected effect on the hearer

Will see 2 types of speech acts:
1. Directive acts (requesting information)
2. Expressive acts (sharing an opinion)


>[!definition] Meaning
>An abstract representation of explicit signals such as speech, text, gestures…


### Sequential modeling

Given a **word** sequence $X$ to **label** sequence $Y$ 

>[!note] example
>![[NLU-Sentence.pdf#page=19&rect=96,276,801,374|NLU-Sentence, p.19]]
>Done one-by-one, *morning* and *Tuesday* will not get linked

Two basic tasks:
1. Segmentation
2. Labelling

#### Human knowledge approach
 - aka Grammar Writing

For example using regular expressions

#### Learning from data
Using ML to learn from annotated examples

Sometimes target data is more structured
![[NLU-Sentence.pdf#page=25&rect=123,55,722,303|NLU-Sentence, p.25]]

#### Semantics of labels

1. ontology
	- Degrees of abstractness
2. Frames
	- Linguistic motivation

#### In practice: Intent

Conversational services have a dictionary of intents, they will map the user query to one of the given intents
[[NLU-Sentence.pdf#page=33&rect=66,139,786,547|NLU-Sentence, p.33]]

Problems with arbitrary intent labels

>[!warning] Terminology
>The choice of labels is essential, yet there are many possible variations in definitions and understanding of the different labels

