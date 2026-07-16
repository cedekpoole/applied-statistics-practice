# Data Analysis Workflow

This guide is built one checkpoint at a time. Only add a section after the
underlying ideas can be explained without prompts.

## Checkpoint 1: Frame The Analysis

Do this before analysing a dataset. It establishes what the data represents,
whether it can answer the question, and what conclusions are defensible.

### The sequence

1. **State the purpose**
   - Say what you are trying to learn or decide.
   - Without a purpose, analysis becomes a collection of unrelated commands.

2. **Check provenance**
   - Record who collected the original data, where, when, why, and how.
   - Distinguish the original source from the location where the file was downloaded.
   - Check whether the current file is raw, cleaned, or simplified.

3. **Define the unit of observation**
   - Ask: what does one row represent?
   - A row may represent a person, animal, transaction, organisation, place, or event.
   - For Palmer Penguins, one row represents one penguin.

4. **Define the sample and possible population**
   - **Sample:** the observations actually recorded.
   - **Population:** the larger group the analysis aims to understand.
   - Do not assume the sample represents a population without understanding how it was selected.
   - Sample size alone does not guarantee representativeness.

5. **Identify the study type**
   - **Observational study:** researchers record what already exists.
   - **Experiment:** researchers deliberately assign conditions called treatments.
   - A treatment group receives a condition being investigated; a control group provides a comparison.

6. **Set the scope of the conclusions**
   - **Association:** two variables differ or vary together.
   - **Causation:** changing one variable produces a change in another.
   - Observational data can show associations but does not, by itself, establish causation.
   - Consider whether another variable could explain an apparent relationship.

7. **State one precise analysis question**
   - Name the observations, measurement of interest, and comparison or relationship.
   - Example: "Among the penguins recorded in this study, how does body mass differ between species?"

8. **Identify each variable's role and type**
   - **Outcome variable:** the measurement being compared, explained, or predicted.
   - **Grouping variable:** a categorical variable defining the groups being compared.
   - In the example, `body_mass_g` is the numerical outcome and `species` is the categorical grouping variable.

### Confounding

A confounder is another variable that may help explain an apparent relationship.
For a variable to confound a relationship, it must be connected to both the proposed
explanation and the outcome.

Example: sex could partly explain an island difference in body mass only if:

- sex proportions differ between islands; and
- body mass differs by sex.

### Fit For Purpose

Data is fit for purpose when it contains suitable observations and variables for the
specific question and is sufficiently reliable and complete. Fitness is
question-specific: a dataset may answer one question but be unsuitable for another.

### Common mistakes

- Treating a download URL as the original data source.
- Failing to define what one row represents.
- Calling the sample "the population".
- Assuming a larger sample is automatically representative.
- Generalising to species, places, ages, or periods not covered by the data.
- Describing an observational association as a causal effect.
- Calling every explanatory variable a grouping variable; grouping variables are categorical.

### Interview explanation

> Before analysing, I establish the question, provenance, unit of observation,
> sample, possible target population, and study design. I then identify the
> variables and their roles and check whether the data is fit for the question.
> This determines which methods are appropriate and prevents unsupported causal
> or population-level claims.

### Recall check

Without notes, be able to answer:

- What does one row represent?
- What is the difference between a sample and a population?
- What distinguishes an observational study from an experiment?
- What is a treatment?
- What is the difference between association and causation?
- When could a third variable be a confounder?
- Why does provenance matter?
- What makes data fit for purpose?
