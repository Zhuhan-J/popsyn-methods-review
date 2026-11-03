# Populations with social networks

Ties across households matter for joint activities, social influence and information spread. This page covers methods that add ties to a synthetic population and methods that build whole networks from ego-network surveys, where each respondent (ego) names contacts (alters) and says which know each other.

## What ego-network data can and cannot show

*My reading.* An ego-network survey samples people through ties. Egos are in the data because they answered; alters are in it only because an ego named them. The survey observes each ego's ties to everyone (named people are tied to the ego, others are not) and the ties among the alters as the ego reports them. It does not observe the alters' other ties, any tie between two ego networks, or the global network the ego networks come from.

Five consequences follow. First, ties are over-represented: many pairs inside an ego network are tied, almost none in a population, so a model trained only on pairs inside ego networks never sees two strangers. Second, ego networks cannot be joined into one whole network. Third, a tie means whatever the name generator asked for, often capped, and a synthetic network should use the same definition. Fourth, alter-alter ties are proxy reports by the ego. Fifth, the egos may not be a random sample of the population.

## Rule-based networks

### Jiang et al. (2022)

*Computational Urban Science*, 2, 7. https://doi.org/10.1007/s43762-022-00034-1

*What the paper reports.* A geographically explicit population with social networks for the New York metro area, built by synthetic reconstruction from the 2010 US Census, with workplaces from LEHD origin-destination data and schools by age and distance. Ties link people who share a household, workplace or school: households are fully connected, and groups larger than five get a Newman-Watts small-world network. The [code](https://bit.ly/SynPopABM) and [data](https://osf.io/3vsaj/) are public.

*My reading.* A generator that takes the population as given and adds ties by rule.

### Jiang et al. (2024)

*Scientific Data*, 11, 1204. https://doi.org/10.1038/s41597-024-03970-1

*What the paper reports.* The same approach for the whole United States from the 2020 Census: 308,745,538 individuals with stylized home, work and school networks, checked against ACS PUMS and block-group data.

## Random-utility tie formation

### Arentze, van den Berg & Timmermans (2012)

*Environment and Planning A*, 44(5), 1101-1120. [Publisher page](https://journals.sagepub.com/doi/10.1068/a4438)

*What the paper reports.* A friendship-formation model for every pair, based on attribute similarity, distance and thresholds for opportunity and base preference, generates a population's complete friendship network. Estimated on survey data from Eindhoven, it generates networks that match the sample's structural properties.

### Arentze, Kowald & Axhausen (2013)

*Social Networks*, 35(3), 451-459. [Publisher page](https://www.sciencedirect.com/science/article/abs/pii/S0378873313000476)

*What the paper reports.* An agent-based random-utility model of friendship formation and social encounters, both with transitivity. Estimated from personal network data in Switzerland, it reproduces geographic proximity, homophily, degree distribution and clustering.

*My reading.* A tie's utility rises with each attribute two people share, and a logistic error turns it into a probability. Without the distance and transitivity terms this is the simplest ERGM, a logistic regression over pairs. Both traditions rest on attribute homophily, with each attribute adding its own match bonus.

## Tie formation from ego-level distributions

### Hannon et al. (2023)

Presentation at the 11th hEART Symposium, Zurich, September 6, 2023.

*What the paper reports.* Ties are formed for a 5% sample of the Munich region's synthetic population (202,041 agents). Each ego's degree, share of same-gender friends and age gaps are drawn from kernel density estimates by age and gender, fitted to the ETH Zurich snowball survey; tie probability decays with distance as a power law, and clique sizes follow an exponential distribution. The network has small-world properties and a transitivity of 0.223, matches men's gender homophily more closely than women's, and spreads age gaps wider than observed.

## ERGMs and connected populations

### Krivitsky & Morris (2017): `ergm.ego`

*The Annals of Applied Statistics*, 11(1), 427-455. https://doi.org/10.1214/16-AOAS1010

*What the paper reports.* Inference for exponential-family random graph models (ERGMs) from egocentrically sampled data, so that a model fitted to ego samples can be simulated as a whole network, implemented in the R package `ergm.ego`.

*My reading.* `ergm.ego` simulates on a population built by copying the surveyed egos, which gives it no household structure and no attribute combinations beyond the sample. Its simplest model, with `edges`, `nodefactor` and `nodematch` terms, is dyad independent, and its likelihood is a logistic regression over pairs. Models with degree or closure terms are dyad dependent: a tie's probability depends on the rest of the network, so they are checked by simulating whole networks, not by scoring pairs.

### Zhang et al. (2019): connected population synthesis

*Transportation Research Part C*, 103, 1-16. [Publisher page](https://www.sciencedirect.com/science/article/abs/pii/S0968090X18318515)

*What the paper reports.* Travel survey data and digital records of networking and behavior are combined into a connected synthetic population for transportation simulation: a Bayesian network for the population and an ERGM calibrated on phone records for the ties.

*My reading.* The tie model takes the population as given and scores ties by attribute similarity.

## Whole networks from ego networks

### Smith (2012): ego network configuration (ENC)

*Sociological Methodology*, 42, 155-205.

*What the paper reports.* Local properties measured in sampled ego networks are used to simulate a whole network consistent with them, and the paper tests how well whole-network properties are recovered from ego-network information alone.

### Conti, Passarella & Pezzoni (2011): a model of ego networks

IEEE WoWMoM 2011, Workshop on Autonomic and Opportunistic Communications, Lucca, Italy, 1-6. https://doi.org/10.1109/WoWMoM.2011.5986141

*What the paper reports.* Emotional closeness relates to the time invested in a relationship, and a time budget for socializing limits network size. A constructive algorithm built on this generates networks that, on average, show the known size, composition and layered structure of ego networks.

## Network dynamics

### Snijders, van de Bunt & Steglich (2010): stochastic actor-based models

*Social Networks*, 32, 44-60. https://doi.org/10.1016/j.socnet.2009.02.004

*What the paper reports.* A tutorial on models in which a directed network evolves as a Markov process driven by actors changing their outgoing ties, depending on the current network and on actor and dyadic covariates; an extension models networks and behavior together.

## Across this stream

*My reading.* Network generators take the population as given: random-utility tie formation, ERGMs and ENC need a complete set of nodes with every attribute filled in, and they only add ties. Yet ties need a complete population. Survey respondents are mostly not each other's contacts, so ties added to a survey sample would connect people who mostly never meet; a full-size synthetic population avoids this and, unlike the raw survey, can be shared. Scoring homophily one attribute at a time also misses how friendships form: ties arise through things no survey records, and two people who match on every recorded attribute may never meet.
