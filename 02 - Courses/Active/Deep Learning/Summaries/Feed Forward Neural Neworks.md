## Maximum Likelihood Principle

Find the $\theta$ which maximizes the probability $p(x|\theta)$ 
- means maximizing the likelihood of the observed data

$\theta_{ML} = \underset{\theta}{\operatorname{argmax}} P_{model}(X|\theta)=\underset{\theta}{\operatorname{argmax}}\prod_{i=1}^Np_{model}(x^{(i)}|\theta)$ 
Use log-likelihood for numerical consistency:

$\theta_{ML} = \underset{\theta}{\operatorname{argmax}}\sum_{i=1}^Np_{model}(x^{(i)}|\theta)$ 
(Equivalent to considering the expected value of the log probability)

>[!definition]- Maximum Likelihood Estimation Steps
>1. Given the likelihood function $\sum_{i=1}^N log P_{model}(x^{(i)}|\theta)$ 
>2. Take derivative and set to 0
>3. Solve for $\theta$

MLE can be generalized to a conditional probability to enable supervised learning. $p(y|x, \theta)$ 
