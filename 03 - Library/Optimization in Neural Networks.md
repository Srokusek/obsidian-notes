
The basic idea is that we are trying to minimize the empirical error that the network is going to make. We can pose it as a non-convex empirical risk minimization problem.

$$\min_{w} \frac{1}{N} \sum_{i=1}^N f_i (w)$$
where $f_i$ is the loss function for the $i^{th}$ data point, $w$ are the weights of the network and the expectation is taken over the data. 

See in [[Stochastic Gradient Descent]]