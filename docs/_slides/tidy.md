---
---

## Tidy data concept

R developer Hadley Wickham (author of the tidyr, dplyr and ggplot packages, among others) defines tidy datasets ([Wickham 2014](http://www.jstatsoft.org/v59/i10/paper)) as those where:

* each variable forms a column
* each observation forms a row
* each type of observational unit forms a table

These guidelines may be familiar to some of you---they closely map to best practices in database design.
{:.notes}

===

Conser a data frame where the outcome of an experiment has been *recorded* in a perfectly appropriate way:

trial | drug_A | drug_B | placebo
    1 |   0.22 |   0.58 |    0.31
    2 |   0.12 |   0.98 |    0.47
    3 |   0.42 |   0.19 |     0.4

===



~~~r
response
~~~
{:.input}
~~~
  trial drug_A drug_B placebo
1     1   0.22   0.58    0.31
2     2   0.12   0.98    0.47
3     3   0.42   0.19    0.40
~~~
{:.output}

===

The response data are present in a compact matrix, as you might record it on a spreadsheet. The form does not match how we think about a statistical model, such as:

~~~r
response ~ treatment + trial
~~~
{:.output}

===

In a tidy format, each row is a complete observation: it includes one response value and all the predictor values. In this data, some of those values are column headers, so we've got to tidy up!

<!--
===

Question
: How would you structure this data in a tidy format as defined above?

Answer
: {:.fragment} Currently, `response` has multiple observations in each row: the observed response in the treatment group and in the control group. For analysis, the data should include a categorical variable for treatment vs. control. 

To put it another way, if your analysis needs some set of names that are found in the column headers to serve as predictors, then you've got to tidy up!
{:.notes}
-->
