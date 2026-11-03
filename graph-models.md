# Graph generative and latent space models

These models from statistics and machine learning mostly work on one observed graph or a set of example graphs; I read them for how they represent ties, scale and are evaluated.

## Latent space models

### Hoff, Raftery & Handcock (2002)

*Journal of the American Statistical Association*, 97(460), 1090-1098.

*What the paper reports.* Each actor has a position in an unobserved social space. The probability of a tie depends on the distance between positions and on covariates, and ties are independent given the positions. Two people close to a third are close to each other, so the model accounts for transitivity.

*My reading.* With a fixed kernel, clustering can fall as the population grows while the mean number of ties stays fixed.

### Raftery, Niu, Hoff & Yeung (2012): case-control likelihood

*Journal of Computational and Graphical Statistics*, 21(4), 901-919. [Publisher page](https://www.tandfonline.com/doi/abs/10.1080/10618600.2012.679240)

*What the paper reports.* The latent space likelihood has a term for every pair of actors. A case-control approximation keeps the ties and subsamples the non-ties, so the model can be fitted to large networks.

*My reading.* In a logistic tie model, when non-ties are subsampled at random, their share in training shifts only the overall level of tie probabilities.

### Ma, Ma & Yuan (2020): fitting latent space models at scale

*Journal of Machine Learning Research*, 21(4), 1-67. [JMLR page](https://jmlr.org/papers/v21/17-470.html)

*What the paper reports.* Two algorithms, nuclear-norm penalization and projected gradient descent, fit inner-product latent space models with edge covariates, with proven convergence rates; the theory extends to distance models.

## Graph autoencoders and node features

### Kipf & Welling (2016): VGAE

NIPS 2016 Workshop on Bayesian Deep Learning. https://arxiv.org/abs/1611.07308

*What the paper reports.* A graph convolutional encoder maps node features and adjacency to Gaussian embeddings, and an inner-product decoder reconstructs ties. On Cora, Citeseer and Pubmed it predicts held-out links well by AUC and average precision, and node features help.

### Salha et al. (2021): FastGAE

*Neural Networks*, 142, 1-19. [arXiv](https://arxiv.org/abs/2002.01910)

*What the paper reports.* Decoding a random subgraph at each training step lets graph autoencoders scale to large graphs with equal or better embeddings.

### Ding, Li, Bhanushali & Liu (2019): DOMINANT

*SIAM International Conference on Data Mining*, 594-602. [Publisher page](https://epubs.siam.org/doi/10.1137/1.9781611975673.67)

*What the paper reports.* An autoencoder for attributed graphs reconstructs node attributes as well as structure and flags anomalous nodes by their reconstruction errors.

### Said et al. (2023): ego-centric subgraph features

*22nd IEEE International Conference on Machine Learning and Applications (ICMLA)*.

*What the paper reports.* When node features are missing or unreliable, ESGEA adds topology-aware features from spectral embeddings of each node's ego-centric subgraph.

### Chen et al. (2024): LLaGA

ICML 2024. https://arxiv.org/abs/2402.08170

*What the paper reports.* Nodes become structure-aware sequences projected into a language model's token embedding space, so one model handles several graph tasks and datasets, including unseen ones.

## Whole-graph generators

### Simonovsky & Komodakis (2018): GraphVAE

ICANN 2018, *Lecture Notes in Computer Science*, 11139, 412-422.

*What the paper reports.* A VAE outputs, in one shot, a probabilistic fully connected graph of a predefined maximum size with node and edge attributes, using approximate graph matching in the loss, for small graphs such as molecules.

### You et al. (2018): GraphRNN

ICML 2018. [PDF](https://www-cs.stanford.edu/people/jure/pubs/graphrnn-icml18.pdf)

*What the paper reports.* Learned from example graphs, GraphRNN adds nodes and edges one at a time, conditioned on the graph so far. By maximum mean discrepancy (MMD) between distributions of graph statistics, it beats the baselines, and it scales to graphs about 50 times larger than earlier deep generative models.

## Network size and sampling in ERGMs

### Krivitsky, Handcock & Morris (2011): the network-size offset

*Statistical Methodology*, 8(4), 319-339. [arXiv](https://arxiv.org/abs/1004.5328)

*What the paper reports.* An unadjusted ERGM keeps density fixed as the network grows, which is often inappropriate for social networks. An offset keeps the mean degree stable while composition changes, which makes the models usable with egocentrically sampled data.

*My reading.* The offset follows from a sparse logistic tie model with logit $s_{ij} + b$. When ties are rare, the intercept $b$ is strongly negative and

```math
\sigma(s_{ij} + b) \approx e^{b} e^{s_{ij}}, \qquad \text{expected ties per person} \approx (N - 1)\, e^{b}\, \mathbb{E}\left[e^{s}\right]
```

so keeping the mean number of ties fixed as the population grows from $N_0$ to $N$ means

```math
b_N \approx b_{N_0} - \log\left(N / N_0\right)
```

### Butts & Almquist (2015): a flexible mean-degree parameterization

*Journal of Mathematical Sociology*, 39(3), 163-167. [PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC4563278/)

*What the paper reports.* A parameterization in which baseline expected degree scales as any power of network size, implemented by adding the edge count and the edge count times log network size as terms.

### Shalizi & Rinaldo (2013): consistency under sampling

*Annals of Statistics*, 41(2), 508-535. [arXiv](https://arxiv.org/abs/1111.3054)

*What the paper reports.* Fitting a network model to a sampled subnetwork assumes consistency under sampling, which many popular ERGM specifications violate; meeting it sharply limits what the models can express. The paper gives conditions for consistent maximum likelihood estimation.
