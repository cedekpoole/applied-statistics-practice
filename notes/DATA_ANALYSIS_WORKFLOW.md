# Data Analysis Workflow Guide

Use this every time you start a new dataset. The goal is to know:

- what question you are answering
- what variables you need
- whether the data is usable
- what method fits the question
- what you can and cannot conclude

## 1. State The Question

Do this first.

```markdown
Question: Do physical measurements differ between penguin species?
```

Why:

- The question decides what code you need.
- Without a question, EDA becomes random.
- In interviews, they want to see that you can connect code to a purpose.

Identify the variables:

```text
Outcome: physical measurements such as body_mass_g
Group/predictor: species
Outcome type: numeric
Group/predictor type: categorical
```

Key terms:

- Outcome: the thing you are trying to explain, compare, or predict.
- Predictor: the variable used to explain or predict the outcome.
- Grouping variable: a categorical variable used to compare groups.

Interview wording:

> I start by defining the question and identifying the outcome, predictor, and variable types. That tells me what summaries, plots, and statistical methods are appropriate.

## 2. Load The Data

```python
from pathlib import Path

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats
import statsmodels.formula.api as smf

PROJECT_ROOT = Path.cwd().parent
DATA_RAW = PROJECT_ROOT / "data" / "raw"

data_path = DATA_RAW / "penguins.csv"
df = pd.read_csv(data_path)

df.head()
```

What you are doing:

- Reading the CSV into pandas.
- Looking at the first few rows.

Why:

- You need to check the file loaded correctly.
- You need to see what one row represents.
- You need to check whether the columns look sensible.

Look for:

- Does each row represent one observation?
- Are column names readable?
- Do values look plausible?
- Are missing values visible?

## 3. Check Shape And Columns

```python
print(f"Shape: {df.shape}")
print(df.columns.tolist())
```

What you are doing:

- Checking the number of rows and columns.
- Listing the column names.

Why:

- Rows tell you the sample size.
- Columns tell you what variables are available.
- Unexpected row or column counts can reveal loading problems.

Look for:

- Does the row count match what you expected?
- Are important variables present?
- Are there duplicate-looking or unclear column names?

Interview wording:

> I check the shape and columns first so I know the size of the dataset and whether the variables needed for the analysis are present.

## 4. Check Data Types

```python
df.info()
```

What you are doing:

- Checking whether each column is numeric, text, date/time, or another type.
- Checking non-null counts.

Why:

- Statistical methods depend on variable type.
- A numeric-looking column may have loaded as text.
- A number is not always a true measurement.

Important:

- Numeric: values are measurements or counts, such as body mass.
- Categorical: values are groups or labels, such as species.
- Binary: categorical with two values, such as yes/no or male/female.
- Date/time: often needs special handling.

Common mistake:

> Treating every integer as a continuous numeric variable. Year, ID, postcode, and binary flags may be stored as numbers but should not be treated like measurements.

## 5. Check Missing Values

Start with missing counts:

```python
df.isna().sum()
```

Then missing percentages:

```python
(df.isna().mean() * 100).round(2)
```

Inspect rows with missing values when the dataset is small:

```python
df[df.isna().any(axis=1)]
```

What you are doing:

- Counting missing values by column.
- Checking how much data is missing.
- Looking at the rows affected.

Why:

- Missing data can reduce sample size.
- Missing data can bias results if it is not random.
- Some analyses only need some columns, so global row dropping can be wasteful.

For larger datasets, check only the columns needed for the question:

```python
important_cols = ["species", "body_mass_g"]

df[important_cols].isna().sum()
df[df[important_cols].isna().any(axis=1)]
```

To check rows missing all key measurements:

```python
measurement_cols = [
    "bill_length_mm",
    "bill_depth_mm",
    "flipper_length_mm",
    "body_mass_g"
]

df[df[measurement_cols].isna().all(axis=1)]
```

Decision rule:

- If missingness is tiny, handle it per analysis.
- If missingness is large, investigate patterns before deciding.
- Do not drop all missing rows automatically.

Interview wording:

> I check both the amount and location of missing data. I avoid blindly dropping rows because missingness may affect only some analyses and could introduce bias.

## 6. Define Column Groups

```python
measurement_cols = [
    "bill_length_mm",
    "bill_depth_mm",
    "flipper_length_mm",
    "body_mass_g"
]

categorical_cols = ["species", "island", "sex"]
```

What you are doing:

- Creating lists of columns you will reuse.

Why:

- It keeps code readable.
- It makes your intent clear.
- It prevents repeated typing mistakes.

Important:

> These lists should be based on meaning, not only pandas data types.

## 7. Overall Descriptive Statistics

Numeric summaries:

```python
df[measurement_cols].describe().T.round(2)
```

Categorical counts:

```python
for col in categorical_cols:
    print(f"\n{col}")
    print(df[col].value_counts(dropna=False))
```

What you are doing:

- Summarising numeric variables.
- Counting categories.

Why:

- You need to understand the dataset before answering specific questions.
- You can spot odd values, possible outliers, and imbalanced groups.

What the numeric summary means:

- count: number of non-missing values
- mean: average
- std: typical spread around the mean
- min/max: smallest and largest values
- 25%, 50%, 75%: quartiles
- 50%: median

Look for:

- Are values plausible?
- Are mean and median very different?
- Is the spread large?
- Are group sizes very uneven?
- Are missing categories shown?

Interview wording:

> I use descriptive statistics to check ranges, typical values, spread, and group sizes before formal testing or modelling.

## 8. Analyse One Question At A Time

Use this rhythm for every question:

```text
1. State the question
2. Identify outcome and predictor/group variables
3. Select only the columns needed
4. Drop missing values only for those columns
5. Check sample size and group sizes
6. Summarise the relevant variables
7. Visualise the relationship or comparison
8. Choose the method
9. Check assumptions
10. Run the test or model
11. Interpret carefully
12. State limitations
```

Why:

- It keeps the notebook readable.
- It stops you from running tests without context.
- It mirrors how you should explain your work in interviews.

## 9. Group Comparison Pattern

Use when asking:

> Do groups differ on a numeric outcome?

Example:

> Do species differ in body mass?

Variables:

```text
Outcome: body_mass_g
Group: species
Outcome type: numeric
Group type: categorical
```

Code:

```python
outcome = "body_mass_g"
group = "species"

analysis_data = df[[group, outcome]].dropna()

analysis_data[group].value_counts()
```

Then:

```python
analysis_data.groupby(group)[outcome].agg(
    ["count", "mean", "std", "median"]
).round(2)
```

Why:

- You need group sizes before comparing groups.
- You need means and medians to see the direction of differences.
- You need standard deviations to judge how variable the groups are.

Visualise:

```python
sns.boxplot(data=analysis_data, x=group, y=outcome)
sns.stripplot(data=analysis_data, x=group, y=outcome, color="black", alpha=0.4)
plt.show()
```

Why:

- Boxplots show median, spread, and possible outliers.
- Stripplots show the actual observations.

Method choice:

```text
Two groups:
- t-test if assumptions are reasonable
- Mann-Whitney U if using a non-parametric alternative

Three or more groups:
- ANOVA if assumptions are reasonable
- Kruskal-Wallis if using a non-parametric alternative
```

Assumptions to think about:

- observations are independent
- outcome is roughly normal within groups
- group variances are not wildly different
- no extreme outliers dominating the result

Careful interpretation:

> The summaries suggest that body mass differs by species. A formal test is needed to assess whether the observed differences are larger than we would expect from sampling variation alone.

## 10. Numeric Relationship Pattern

Use when asking:

> Are two numeric variables related?

Example:

> Are flipper length and body mass related?

Variables:

```text
Variable 1: flipper_length_mm
Variable 2: body_mass_g
Both are numeric
```

Code:

```python
x = "flipper_length_mm"
y = "body_mass_g"

analysis_data = df[[x, y]].dropna()

analysis_data[[x, y]].describe().T.round(2)
```

Visualise:

```python
sns.scatterplot(data=analysis_data, x=x, y=y)
plt.show()
```

Why:

- A scatterplot shows direction, shape, strength, and outliers.
- Correlation alone can hide non-linear patterns.

Method choice:

```text
Pearson:
- measures linear relationship
- sensitive to outliers

Spearman:
- measures monotonic relationship
- uses ranks
- less sensitive to non-normality
```

Careful interpretation:

> A correlation describes association, not causation.

## 11. Categorical Association Pattern

Use when asking:

> Are two categorical variables associated?

Example:

> Is species associated with island?

Variables:

```text
Variable 1: species
Variable 2: island
Both are categorical
```

Code:

```python
row_var = "species"
col_var = "island"

table = pd.crosstab(df[row_var], df[col_var])
table
```

Proportions:

```python
pd.crosstab(df[row_var], df[col_var], normalize="index").round(2)
```

Why:

- Counts show the raw distribution.
- Proportions make group patterns easier to compare.

Method choice:

```text
Chi-square test of independence
```

Assumptions to think about:

- observations are independent
- expected cell counts should not be too small

Careful interpretation:

> A chi-square test can show association between categories, but it does not explain cause.

## 12. Simple Linear Regression Pattern

Use when asking:

> Can one numeric variable explain variation in another numeric variable?

Example:

> Can flipper length explain variation in body mass?

Variables:

```text
Outcome: body_mass_g
Predictor: flipper_length_mm
Both numeric
```

Code:

```python
model_data = df[["body_mass_g", "flipper_length_mm"]].dropna()

model = smf.ols("body_mass_g ~ flipper_length_mm", data=model_data).fit()
model.summary()
```

What to inspect:

- coefficient: expected change in outcome for one-unit increase in predictor
- p-value: evidence against no association
- confidence interval: plausible range for the coefficient
- R-squared: proportion of variation explained
- residuals: model errors

Assumptions to think about:

- linear relationship
- independent observations
- residuals roughly normal
- constant variance of residuals
- no extreme influential outliers

Careful interpretation:

> The model estimates an association between flipper length and body mass. It does not prove that flipper length causes body mass.

## 13. Method Choice Cheat Sheet

```text
Numeric outcome + two groups:
    t-test or Mann-Whitney U

Numeric outcome + three or more groups:
    ANOVA or Kruskal-Wallis

Numeric variable + numeric variable:
    Pearson/Spearman correlation
    simple linear regression

Categorical variable + categorical variable:
    chi-square test

Binary outcome:
    logistic regression
```

## 14. Interpretation Template

Use this after every test or model:

```text
Question:

Variables:

Method:

Why this method:

Result:

Plain-English interpretation:

Assumptions/limitations:

What I cannot claim:
```

Example:

```text
Question: Do species differ in body mass?
Variables: body_mass_g and species
Method: ANOVA
Why this method: body_mass_g is numeric and species has three groups
Result: p-value was below 0.05
Plain-English interpretation: body mass differs between at least some species in this sample
Assumptions/limitations: assumes independent observations, approximate normality, and similar variances
What I cannot claim: this does not prove species causes body mass differences in a causal sense
```

## 15. Final Checklist

Before moving on, ask:

- Have I stated the question?
- Have I identified the outcome and predictor/group variables?
- Have I checked shape and columns?
- Have I checked data types?
- Have I checked missing values?
- Have I handled missing values only for the analysis I am doing?
- Have I checked group sizes or sample size?
- Have I looked at descriptive statistics?
- Have I made a relevant plot?
- Have I chosen a method that matches the variable types?
- Have I considered assumptions?
- Have I interpreted the result in plain English?
- Have I avoided overclaiming?

