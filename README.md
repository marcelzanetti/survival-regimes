# survival-regimes

Which factors most influence the end (or survival) of a political regime? 

This is the central question we try to investigate here, using statistical methods of survival analysis.  Of course, the subject has been widely discussed and studied by political scientists in the last decades, and several meaningful conclusions, made up from particularly different methodologies, have already been published. The exploratory analysis and modelling we did here doesn't aim neither to present conclusive insights nor to be treated as a solid and detailed, or even original, study. We just wanted to deliver some data analysis and prediction into a particularly interesting subject.

## The data

We came up with two public datasets. 

The first one includes duration and another characteristics (type, leader, country, etc.) of political regimes throughout the world between 1946 and 2008. It can be found in: *Cheibub, José Antonio, Jennifer Gandhi, and James Raymond Vreeland. 2010. “Democracy and Dictatorship Revisited.” Public Choice, vol. 143, no. 2-1, pp. 67-101.*, or accessed throug *lifelines* package.

The second one, from Harvard Business School, presents global crisis data by country 1800 and 2016, and it can be [found here](https://www.hbs.edu/behavioral-finance-and-financial-stability/data/Pages/global.aspx).

### Regime Data

The notebook with regime data exploration is [here](https://github.com/marcelzanetti/survival-regimes/blob/main/survival_regimes/ExploringRegimes.ipynb).

For each political term, we get informations such as the political leader, year of beginning and duration, country (region and continent), whether it is a democracy and which is its regime type. A snapshot of the dataset:

```python
regime_df.sample(n=5)
```
|       country_name |  country_region | country_continent |               gov_leader | regime_is_democracy |       regime_type | gov_start | gov_duration | gov_observed_regular_end |
|-------------------:|----------------:|------------------:|-------------------------:|--------------------:|------------------:|----------:|-------------:|-------------------------:|
|         Bangladesh |   Southern Asia |              Asia |            Zia ur-Rahman |       Non-democracy |     Military Dict |      1977 |            4 |                        0 |
|     United Kingdom | Northern Europe |            Europe |               Tony Blair |           Democracy | Parliamentary Dem |      1997 |           10 |                        1 |
|            Uruguay |   South America |          Americas |           Benito Nardone |           Democracy |  Presidential Dem |      1960 |            1 |                        1 |
| Dominican Republic |       Caribbean |          Americas |         Joaquin Balaguer |           Democracy |  Presidential Dem |      1986 |           10 |                        1 |
|         Mozambique |  Eastern Africa |            Africa | Armando Emïlio Guebuza   |       Non-democracy |     Civilian Dict |      2005 |            4 |                        0 |

#### Estimating the survival function

Kaplan-Meier is a non-parametric estimator for the survival function. Based on our intuition, we would say that, at a given time *t*, the survival function of democracy regimes' lifetime would be lower than the survival function, for this same time *t*, of a dictatorial regime's lifetime; that is, we surely expect, at least, that democratic political terms end before dictatorial ones, in general.

We first get the Kaplan-Meier estimate for all the data, i.e., the general survival function.
![general_km](https://user-images.githubusercontent.com/71240129/172071231-894a90ae-67fd-4b0b-b505-92f585ecca62.png)

By the end of five years, more than half of the terms will have died and only one in five last longer than ten years, for example.

Now we split this into continent, regime type and presence of democracy.

##### Survival function by continent

![continent_km](https://user-images.githubusercontent.com/71240129/172071742-a9b079e7-4694-46da-88da-39fb93604874.png)

##### Survival function by presence of democracy
![democracy_km](https://user-images.githubusercontent.com/71240129/172071782-4d8ee7eb-6cf7-4f99-aac4-48ce33978db2.png)

##### Survival function by regime type

![regimetype_km](https://user-images.githubusercontent.com/71240129/172071780-90128631-96c1-4610-9913-7518569aeac0.png)

### Crisis Data

For each country, we get information on the presence of economic crisis in each year (1800-2016). These economic crisis include: banking crisis, systemic crisis, default in domestic and sovereign debt, currency crisis and inflation crisis (we also get inflation index for the reference year). 

Notebooks with manipulate and explore crisis data are [this](https://github.com/marcelzanetti/survival-regimes/blob/main/survival_regimes/ExploringCrisis.ipynb) and [this](https://github.com/marcelzanetti/survival-regimes/blob/main/survival_regimes/FeatureEngineering.ipynb).

We need to agreggate crisis data, which is in year level, to turn into political term level. To do this, we consolidate each crisis indicator variable into its sum and pondered weight during that period (we give weights proportional to time; so crisis in the end of the term counts more than in the beginning).

## Modelling

We use a Cox model to predict survival hazard. Basically, a Cox model is a linear model that takes covariates and a basis time function to give the hazard rate of survival for each given time. Matematically, this relation can be modelled as following:

![image](https://user-images.githubusercontent.com/71240129/172072153-06766cec-eddb-4b51-93a6-16d5c55786fc.png)
*(source: [lifelines docs](https://lifelines.readthedocs.io/en/latest/Survival%20Regression.html))*

We test several models (methodology and more details are described [here](https://github.com/marcelzanetti/survival-regimes/blob/main/survival_regimes/Modelling.ipynb) and come up with a model that has the following features (and respectives estimates):

![estimates_coef](https://user-images.githubusercontent.com/71240129/172072651-78100c90-4cf5-409b-a129-847f42790051.png)

The *exp_coef* tells us how much the hazard increase when that covariates increases and all else is kept constant. For dummy variables, it's the increase in hazard when that feature appears in the dataset. For example, *pond_crisis_currency* (the weighted average of the times there was currency crisis in that term) has a (exp) coefficient of 5, which means that an increase in one in *pond_crisis_currency* will increase 5 times the hazard ratio. *regime_democracy* has a coefficient of 1.3; so a democratic term has a hazard of ending 30% greather than a non-democratic one.
