# Population synthesis and social-network methods: a review

This is my review of the methods literature behind my PhD work on synthetic populations and social networks, which agent-based travel models need: agents in households, with ties across households. I read three streams of methods and how they are evaluated: population synthesis, which generates households and persons from a survey sample; methods that give a population social ties, including whole networks built from ego-network data; and graph generative and latent space models from statistics and machine learning.

## Reading map

| Stream | Page | What it covers |
|---|---|---|
| Population synthesis | [population-synthesis.md](population-synthesis.md) | Gibbs sampling, the hierarchical latent class model, VAEs and GANs, language models, spatial data fusion |
| Populations with social networks; whole networks from ego-network data | [social-networks.md](social-networks.md) | What ego-network data can show, rule-based networks, random-utility tie formation, tie formation from ego-level distributions, ERGMs and connected population synthesis, ego network configuration, the Conti ego-network model, network dynamics |
| Graph generative and latent space models | [graph-models.md](graph-models.md) | Latent space models, graph autoencoders, whole-graph generators, network size in ERGMs |
| Comparison | [comparison.md](comparison.md) | Method families side by side, and themes across streams |
| Evaluation | [evaluation/metrics.md](evaluation/metrics.md) | Sampling and structural zeros, SRMSE, profile precision, recall and F1, network evaluation, reporting |
| References | [references.md](references.md) | Full citations, adjacent reading, code and tools |

## How entries are organized

Each paper gets a heading, its venue and link, and up to two paragraphs. *What the paper reports* gives the method, data and findings as the authors state them, with numbers as printed. *My reading* holds my interpretation or critique, only where I have one. Full citations are in [references.md](references.md).

Citations follow the published version where one exists, and preprints are marked. Code links and licenses were checked on November 3, 2026; code without a stated license is linked, never copied.
