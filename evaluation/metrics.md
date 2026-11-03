# Evaluating synthetic populations and networks

I define each metric as I compute it.

## Sampling zeros and structural zeros

*What the papers report.* Kim & Bansal (2023) and Lim et al. (2026) sort attribute combinations by whether they appear in the sample, the population and the generated data. Sampling zeros are in the population and the generated data but not the sample, such as a minority group the survey missed. Structural zeros are infeasible combinations found only in the generated data, such as a six-year-old with a driver's license. Missing samples are in the sample but not generated. A generator pushed toward more sampling zeros cannot avoid more structural zeros. Both papers treat a large survey as a hypothetical population and train on a small random subset, so recovered sampling zeros can be counted. Borysov et al. (2019) note that no general test separates plausible new agents from implausible ones.

*My reading.* The hypothetical population design does not carry over to ties. Ego networks cannot be joined into one whole network, so there is no population of ties against which to count recovered sampling zeros.

## SRMSE

*What the papers report.* Borysov et al. (2019), Kim & Bansal (2023) and Lim et al. (2026) compare real and synthetic marginal and bivariate distributions by the standardized root mean squared error (SRMSE); lower is better.

*How I compute it.* Let $\nu_m$ and $\hat\nu_m$ be the real and synthetic shares in cell $m$, with one table per variable (marginal) or pair of variables (bivariate), each normalized to shares and all concatenated into $n_c$ cells:

```math
\mathrm{SRMSE} = \frac{\sqrt{\frac{1}{n_c} \sum_{m=1}^{n_c} \left(\hat\nu_m - \nu_m\right)^2}}{\frac{1}{n_c} \sum_{m=1}^{n_c} \nu_m}
```

Each of the $T$ tables sums to one, so the denominator equals $T / n_c$, and with $S = \sum_m (\hat\nu_m - \nu_m)^2$,

```math
\mathrm{SRMSE} = \frac{\sqrt{S \, n_c}}{T}
```

*My reading.* SRMSE depends on the set of cells. Adding $B$ cells empty in both data sets leaves $S$ unchanged but multiplies SRMSE by $\sqrt{(n_c + B) / n_c}$. Cells built from the categories observed in either data set penalize a category only the synthetic data contain, but two runs that generate different rare categories then have different cell sets. For strict comparison across runs and methods, I fix one cell set, such as the full category set.

## Precision, recall and F1 on attribute profiles

*What the papers report.* Kim & Bansal (2023) adapt precision and recall from image generation. Precision, the share of generated records whose combination exists in the population, measures feasibility (one minus the structural zero rate); recall, the share of population records whose combination is generated, measures diversity. Lim et al. (2026) tune on F1 and also count distinct generated combinations, because recall weights combinations by their frequency.

*How I compute it.* A profile, $\mathrm{prof}(r)$, is the full combination of attributes of record $r$. With $\mathcal{R}$ the real and $\hat{\mathcal{R}}$ the synthetic records:

```math
\mathrm{Prec} = \frac{1}{|\hat{\mathcal{R}}|} \sum_{\hat r \in \hat{\mathcal{R}}} \mathbf{1}\left[\mathrm{prof}(\hat r) \in \mathrm{prof}(\mathcal{R})\right], \qquad
\mathrm{Rec} = \frac{1}{|\mathcal{R}|} \sum_{r \in \mathcal{R}} \mathbf{1}\left[\mathrm{prof}(r) \in \mathrm{prof}(\hat{\mathcal{R}})\right]
```

```math
F_1 = \frac{2 \, \mathrm{Prec} \, \mathrm{Rec}}{\mathrm{Prec} + \mathrm{Rec}}
```

Both sums run over records, so a generator that repeats one common profile gains precision and loses recall.

*My reading.* For households I compute profiles at more than one level: a person with their household's attributes, a household with its respondent, and the respondent paired with each other member. Two generators can tie at the household level and differ at the pair level if only one reproduces structure such as parent-child age gaps. Generators that write whole households also need structural checks: decodable output, household size matching the member count, and coherent relationship and age.

## Evaluating networks

*What the papers report.* Link prediction models such as VGAE (Kipf & Welling, 2016) are scored by AUC and average precision on held-out ties; whole-graph generators such as GraphRNN (You et al., 2018) by maximum mean discrepancy (MMD) between distributions of graph statistics. O'Bray et al. (2022) set criteria for such metrics, test MMD systematically on perturbed graphs and recent models, describe its pitfalls and give practical recommendations.

*My reading.* The first two rules follow from what ego-network data can and cannot show (see [social-networks.md](../social-networks.md#what-ego-network-data-can-and-cannot-show)); the other two apply to any network comparison. First, there is no whole network to test against, and any completed network would be built by a model, which makes a test against it circular. Second, every alter is tied to its ego, so non-tie examples for the ego must come from outside its network, and labeling pairs across ego networks as non-ties is approximately but not exactly true. Third, methods should be compared on statistics none of them was fitted to, and any statistic a method was fitted to should be flagged. Fourth, simulating a fitted model and comparing it with the observed networks, as in ERGM practice, checks goodness of fit on the training data; a held-out test needs unseen data.

## Reporting

*What the papers report.* Kim & Bansal (2023) conclude that distributional similarity can mislead, so feasibility and diversity should be computed too.

*My reading.* Training deep generative models on small samples taught me five rules for training and reporting. First, seed noise can be as large as the differences a tuning rule must resolve, so margins and seed counts should come from measured noise, and seed spread should be reported. Second, a single run can fall below a baseline that the mean over seeds clearly beats, so each setting needs several seeds. Third, the best early-stopping score is optimistic, being the best of many noisy scores; only held-out test scores measure accuracy. Fourth, budgets should count optimizer steps, because an epoch cap gives small training sets far fewer updates. Fifth, a lead seen on a few seeds only justifies planning a check on more seeds.
