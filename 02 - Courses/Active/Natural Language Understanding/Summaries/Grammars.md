slides - [[lecture-grammars.pdf]]
Want to focus on the structural information of language (less focus on the "simple" embeddings)

### Syntactic Parsing

>[!note] Why is structure important?
>parsing the grammar structure properly should allow us to evaluate possible multiple meanings
>![[lecture-grammars.pdf#page=4&rect=44,104,789,385|lecture-grammars, p.4|400]]

=>llm models are not able to parse the structure properly ([[lecture-grammars.pdf]])

>[!warning] Why the ambiguity?
>Parsing the text without clues (such as stress when spoken, context in written test) means that there are multiple possible interpretations
>Noticing these is important for consideration or to know when to ask for clarification



### Semantic Parsing

Focus on questions about the high-level meaning
	Who did What to Whom

Semantic and syntactic representations do not always aligned
![[lecture-grammars.pdf#page=19&rect=104,104,800,197|lecture-grammars, p.19]]

### Grammars

>[!definition] Grammars
>Formal models that describe relations amongst symbolic representations of language.
>Utilize constraints on the word's relative position in a sentence and similar information

Two main uses:
1. **Analysis** of sentences
2. **Generation** of sentences

>[!note]- Elisa Chatbot in 1968
>Designed to behave like a psychiatrist that mostly listens. Rule based chatbot system. Looks for information based on regular expressions, selected the relevant parts of sentences to select responses
>![[lecture-grammars.pdf#page=24&rect=89,169,679,449|lecture-grammars, p.24]]

**Chomsky Language Hierarchy**

different groups abide by different rules
Regular Languages
	$A\arrow \alpha |\alpha B$ 
Context-Free Grammars
	$A \arrow \alpha$ 

![[lecture-grammars.pdf#page=28&rect=94,61,446,403|lecture-grammars, p.28|300]]

#### Context-Free Grammars

captures **constituency** and **ordering**
1. Constituency
	how words group into units (**constituents**)
2. Ordering
	what is the **order** of the words

some key constituents (in English)
**Noun Phrases**, **verb phrases**, **prepositional phrases**, **sentences**

We can state some generalities about the order of the words, although not always absolute 
	Noun phrases mostly before verb phrases but not always

Using grammar rules to generate sentences:
![[lecture-grammars.pdf#page=42&rect=43,74,797,447|lecture-grammars, p.42|500]]

Context Free Grammars really means that we have no context on the left side of the rules
	Actually it is more of a constraint to the language

>[!definition] Parsing
>Parsing is the process of taking a string, a grammar and returning zero or more parse trees for that string.

#### The key English grammar constituents
1. Noun Phrases (NPs) ![[lecture-grammars.pdf#page=82&rect=151,47,703,444|lecture-grammars, p.82|300]]
2. Prepositional Phrases	![[lecture-grammars.pdf#page=83&rect=186,265,357,400|lecture-grammars, p.83|100]]
**Recursions**
Phrases can be nested within themselves
	NP->NP PP
	VP->VP PP
	
>[!cons] 
>Rules like VP->V NP ca lead to problems like:
>![[lecture-grammars.pdf#page=90&rect=139,61,784,409|lecture-grammars, p.90|400]]

3. Sentences
	Different sentence types ![[lecture-grammars.pdf#page=92&rect=151,264,740,418|lecture-grammars, p.92|300]]
	