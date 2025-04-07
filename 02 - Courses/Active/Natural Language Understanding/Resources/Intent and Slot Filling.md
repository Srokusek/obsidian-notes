

## Sequence labeling and shallow parsing

>[!definition] Sequence labeling
>A task of assigning a label to all tokens in a sequence such that the probability of those labels is maximized
>- Given a sequence of tokens $w = {w_1, w_2, ..., w_n}$,
>- defining a sequence of labels as $l = {l_1, l_2, ..., l_n}$
>- compute the sequence $\hat{l}$ such as $\hat{l} = \underset{l}{\operatorname{argmax}} P(l|w)$



## Text classification

>[!definition] Text classification
>Give a label to an entire input sequence (in contrast to individual token classification in sequence labelling)
>- Given a sequence of tokens $w = {w_1, w_2, ..., w_n}$,
>- And a set of labels $L$ where $l \in L$
>- estimate the label $\hat{l}$ such as $\hat{l} = \underset{l}{\operatorname{argmax}} P(l|w)$

# Dataset for the second project

Structure of individual samples:
```json
[
    {
    "utterance": "on april first i need a flight going from phoenix to san diego",
    "slots": "O B-depart_date.month_name B-depart_date.day_number O O O O O O B-fromloc.city_name O B-toloc.city_name I-toloc.city_name",
    "intent": "flight"
    },
    "..."
 ]
```

Need to create a dev-set. Set by picking `portion = 0.10`.

Convert words to ids to be able to access words via their id
 - Defined as the Lang class typically