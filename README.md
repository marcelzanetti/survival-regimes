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
