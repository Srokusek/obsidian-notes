
### Evaluation of NER
- possible to use recall/accuracy

What if only part of the of the entity was classified
![[NE.pdf#page=56&rect=42,313,724,370|NE, p.56]]
 - can be seen as both a false positive **and** false negative
 - How to evaluate?
 - Assigning both errors would mean that selecting nothing would give better score
 
Performance also depends on the genre 
![[NE.pdf#page=60&rect=330,161,811,403|NE, p.60|300]]

Or types
![[NE.pdf#page=62&rect=288,95,817,447|NE, p.62|300]]

=> **evaluation should be done on different splits of data** 
### Nested named entities

>[!note] Nested Named Entites
>Especially important in biology, named entities are are nested within each other, of the shelf libraries will likely do **badly**
>![[NE.pdf#page=64&rect=76,322,798,457|NE, p.64]]

Why important:
- gives additional information (for example the location of Cambridge University)

### Open and domain named entities

>[!note] Open entities
>Entities with open in terms of vocabulary and structure 
>![[NE.pdf#page=70&rect=82,151,788,396|NE, p.70]]

The NER can be parsing either as closed or open 
![[NE.pdf#page=71&rect=357,8,812,367|NE, p.71|400]]

### Training and evaluating NER

Training 
1. Collect **representative** documentS
2. Annotate each named entity type
3. Train sequence labeled

Alternatively:
Use and evaluate an off the shelf model
