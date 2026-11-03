# Population synthesis

Population synthesis turns a small sample, usually a household travel survey covering 1 to 5 percent of the population, into synthetic households and persons. Reweighting and resampling reuse the sample's attribute combinations; generative models create new ones, and much recent work is about keeping those plausible. Sampling and structural zeros are defined in [evaluation/metrics.md](evaluation/metrics.md#sampling-zeros-and-structural-zeros).

## Simulation and pipelines

### Kukic, Li & Bierlaire (2024): one-step Gibbs sampling

*Transportation Research Part C*, 166, 104770. https://doi.org/10.1016/j.trc.2024.104770

*What the paper reports.* All variables of a household form one random vector, with members sorted by decreasing age so that no role labels are needed, and a separate Gibbs sampler runs for each household size. On the 2015 Swiss micro-census this gives a more representative sample and faster generation, and the conditionals can enforce rules against illogical households. The authors find that model-based methods control generation better than machine learning methods.

### Hörl & Balac (2021): an open pipeline for Île-de-France

*Transportation Research Part C*, 130, 103291. https://doi.org/10.1016/j.trc.2021.103291

*What the paper reports.* A replicable process on open data and software generates households, persons and activity chains for Paris and its region, one step after another. The paper also discusses how implicit correlation structures limit the uses of the synthetic demand, and how running downstream simulations on a population sample affects the results.

## Hierarchical latent class model

### Sun, Erath & Cai (2018): HLC

*Transportation Research Part B*, 114, 199-212. https://doi.org/10.1016/j.trb.2018.06.002

*What the paper reports.* Probabilistic tensor factorization models the categorical attributes at each level as a mixture, and a multilevel latent class model links household classes to shared individual classes, estimated by EM with BIC choosing the numbers of classes. The case study uses Singapore's 2012 Household Interview Travel Survey. Members are independent given the household class, so rejection sampling on the two members' age and sex differences restores relationships such as husband and wife in two-person households; the authors note the procedure can be applied to other household types.

*My reading.* The paper demonstrates the rejection step on two-person households only. Extending it raises two problems: the acceptance features must summarize several members (a maximum over members maps very different households to the same bin), and the empirical distributions by household size become sparse, which makes the bound M unstable. HLC also gives every member the same attribute set. When a survey records some attributes only for the respondent, the model has to be modified to treat them as missing for the other members.

### Sun & Axhausen (2016): probabilistic tensor factorization

*Transportation Research Part B*, 91, 511-524. https://doi.org/10.1016/j.trb.2016.06.011

*What the paper reports.* Mobility records are modeled as multivariate categorical observations by a multi-way probabilistic factorization (tensor decomposition with probabilistic latent semantic analysis), estimated by EM on 14 million Singapore smart card journeys. HLC's first component builds on it.

## Deep generative models

### Borysov, Rich & Pereira (2019): VAE

*Transportation Research Part C*, 106, 73-97. https://doi.org/10.1016/j.trc.2019.07.006

*What the paper reports.* On a Danish travel diary, a Gibbs sampler and a Bayesian network beat a VAE when few attributes are modeled but scale poorly as attributes are added. A Gibbs sampler with frequency-table conditionals replicates the sample's agents, while the VAE generates out-of-sample agents, which addresses sampling zeros. Kim & Bansal (2023), Sané et al. (2025) and Lim et al. (2026) also report SRMSE.

### Aemmer & MacKenzie (2022): VAE and CVAE

*Computers, Environment and Urban Systems*, 96, 101852. [Publisher page](https://www.sciencedirect.com/science/article/abs/pii/S0198971522000965)

*What the paper reports.* A VAE generates households, and a conditional VAE generates each household's persons. Against an open-source synthesizer it is more accurate and faster for 4 to 16 variables, and it works with as few as 100 training samples.

*My reading.* In the reference code, conditioning is the decoder taking the latent draw concatenated with the household's one-hot vector. Each member comes from an independent latent draw with the same household input, so members are not conditioned on each other. Continuous attributes must be binned first.

### Kim & Bansal (2023): feasible and diverse generation

*Transportation Research Part C*, 148, 104053. https://doi.org/10.1016/j.trc.2023.104053

*What the paper reports.* Deep generative models recover sampling zeros but create structural zeros. Assuming sampling zeros lie nearer the boundary of the training distribution, measured on the discrete attributes and in a learned embedding space, the authors add a boundary-distance loss against structural zeros and an average-distance loss for missing samples and sampling zeros to a WGAN and a VAE. Trained on a 5% sample of over one million South Korean survey records, the regularized VAE recovers 23.5% of the population the sample misses at 79.2% precision, and the GAN 18.3% at 89.0%. VAEs favor diversity and WGANs feasibility.

The evaluation design, and why it does not carry over to ties, is in [evaluation/metrics.md](evaluation/metrics.md#sampling-zeros-and-structural-zeros).

### Sané et al. (2025): a practical study of VAEs

*Journal of Computational Social Science*, 8, 13. https://doi.org/10.1007/s42001-024-00332-0

*What the paper reports.* A study of architectures, parameters, sample sizes and indicators for VAEs on mixed attributes. Trained on 16,500 individuals, a VAE generated 1.4 million agents with marginal errors under 3 percentage points; with 5,000 it produced about 20% sampling zeros and few structural zeros. Numerical attributes are harder than categorical ones, post-processing is still needed, and households are future work.

### Yang et al. (2025, preprint): ciDATGAN

arXiv:2508.09964. https://arxiv.org/abs/2508.09964

*What the paper reports.* A conditional-input directed acyclic tabular GAN generates household and member attributes together, guided by directed acyclic graphs and a partial PopGen population. For New York State (nearly 20 million people) it matches census marginals, keeps member associations similar to the sample, and is 17% more diverse by entropy than the PUMS sample and 13% more than a PopGen benchmark.

## Language models

### Borisov et al. (2023): GReaT

ICLR 2023. https://arxiv.org/abs/2210.06280

*What the paper reports.* Table rows become text sequences of feature-value pairs in random order for fine-tuning an autoregressive language model, which can then condition on any subset of features. It matches or beats existing generators in the authors' experiments.

### Lim et al. (2026): LLM-BN

*Transportation Research Part C*, 185, 105581. https://doi.org/10.1016/j.trc.2026.105581 (preprint: arXiv:2505.04196)

*What the paper reports.* A distilled GPT-2 is fine-tuned on records written as text, with attributes in topological orderings of a learned Bayesian network; temperature balances feasibility and diversity. On Kim & Bansal's design, LLM-BN reaches about 95% feasibility against roughly 80% for VAE and WGAN at comparable diversity, and F1 of 84.6% (85.2% with random order) against 77.3% (VAE) and 81.1% (WGAN). GPT-4o with few-shot prompts recovers few combinations (recall 50.2%). The fine-tuned models have higher SRMSE, which the authors attribute to favoring coherence across attributes over marginal fit.

## Spatial attributes

### Vo, Kim & Bansal (2025): fusing travel surveys with mobility data

*Transportation Research Part B*, 191, 103128. https://doi.org/10.1016/j.trb.2024.103128

*What the paper reports.* Travel surveys describe people but observe few locations, while passively collected mobility data such as cellular traces cover space with little sociodemographic detail. The method clusters zones to align the sources, splits the fusion into cluster-specific optimizations, and tunes the clustering to balance feasibility (precision) and heterogeneity (recall). In Seoul, the fused population keeps the census distributions while raising spatial heterogeneity, and 97% of it is unobserved in the survey. The review notes that spatial microsimulation, which allocates people to places after synthesizing their sociodemographics, can leave the two inconsistent because the steps run in sequence.

*My reading.* An open question for me: is the inconsistency between simulated sociodemographics and spatial marginals caused by the sequential process?
