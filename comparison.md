# Comparing method families

Cells restate the papers unless marked "(my reading)".

## Population synthesis

| Family | Examples | Generates | Data | Feasibility, diversity, rare combinations | Typical evaluation |
|---|---|---|---|---|---|
| Reweighting, resampling | IPF-type reweighting; prototypical agents in Kim & Bansal (2023), Lim et al. (2026) | Copies of sample households and persons | Microsample; marginals | All feasible, but nothing absent from the sample (zero-cell problem) | Fit to marginals |
| Gibbs sampling | Kukic et al. (2024); baseline in Borysov et al. (2019) | Households or individuals | Microsample | Conditionals enforce rules; frequency-table conditionals replicate the sample | Marginals, conditionals, consistency |
| Hierarchical latent class | Sun et al. (2018) | Households with members | Microsample | Members independent given the household class; rejection corrects two-person households | Marginal and multi-dimensional marginal distributions, which match the sample closely |
| VAE, CVAE, GAN | Borysov et al. (2019); Aemmer & MacKenzie (2022); Kim & Bansal (2023); Sané et al. (2025); Yang et al. (2025) | Individuals; households and persons | Microsample | Sampling zeros at the cost of structural zeros; regularization or post-processing reduces infeasible agents | SRMSE; precision, recall, F1; entropy |
| Language models | Borisov et al. (2023); Lim et al. (2026) | Individuals as text | Microsample; pretrained model | Higher feasibility than VAE and WGAN at similar diversity; higher SRMSE | SRMSE; precision, recall, F1; distinct combinations |
| Spatial data fusion | Vo et al. (2025) | Individuals with home and work locations | Travel survey; mobility data | More spatial heterogeneity, distributions kept | Precision, recall, JSD; census |

## Social ties and graphs

| Family | Examples | Generates | Data | Ties depend on | Population | Typical evaluation |
|---|---|---|---|---|---|---|
| Rules | Jiang et al. (2022, 2024) | Ties in households, workplaces, schools | Census, locations | Shared place | Synthesized first, then taken as given | Census; descriptive network statistics |
| Random-utility tie formation | Arentze et al. (2012, 2013) | Ties | Ego-network survey | Attribute similarity, distance, transitivity; homophily per attribute (my reading) | Taken as given (my reading) | Degree, homophily, clustering, distance decay |
| Distribution matching | Hannon et al. (2023) | Ties | Snowball survey | Each ego's degree, same-gender share and age gaps drawn from distributions by age and gender; distance decay; cliques | Taken as given | Degree, gender homophily, transitivity, distance decay |
| ERGMs | Krivitsky & Morris (2017); Zhang et al. (2019) | Ties | Ego samples; phone records | Model terms; simplest model is a logistic regression over pairs (my reading) | Copied from egos in `ergm.ego` (my reading) | Goodness of fit by simulation |
| Ego network configuration | Smith (2012) | Whole network | Ego-network sample | Local ego-network properties | Taken as given (my reading) | Recovery of whole-network properties |
| Latent space models | Hoff et al. (2002); Raftery et al. (2012); Ma et al. (2020) | Ties | One observed network | Latent positions; transitivity from geometry | Observed actors | Fit to the network |
| Graph autoencoders | Kipf & Welling (2016); Salha et al. (2021); Ding et al. (2019) | Embeddings, ties | One graph with node features | Embeddings | Observed nodes | AUC, average precision; anomaly detection |
| Whole-graph generators | You et al. (2018); Simonovsky & Komodakis (2018) | Whole graphs | Example graphs | Sequential or one-shot decoding | Generated with the graph | MMD on graph statistics |

## Themes across the streams

First, feasibility trades against diversity. Kim & Bansal (2023) and Lim et al. (2026) report that pushing a generator toward sampling zeros raises structural zeros. In my reading, resampling and reweighting sit at the feasible end: every record they produce already exists in the sample, so none is infeasible and none is new.

Second, distribution fit and complete-agent quality can rank methods differently: Kim & Bansal (2023) find that distributional similarity can mislead, and Lim et al. (2026) report higher SRMSE with higher feasibility. In my reading, agent-based models use complete agents, so metrics on complete attribute combinations should weigh more than marginal fit.

Third, in my reading, network generators take the population as given and only add ties, and they score homophily one attribute at a time; see the end of [social-networks.md](social-networks.md#across-this-stream).

Fourth, in my reading, ego-network data over-represent ties and cannot be joined into a whole network, which shapes both training and testing; see [evaluation/metrics.md](evaluation/metrics.md#evaluating-networks).
