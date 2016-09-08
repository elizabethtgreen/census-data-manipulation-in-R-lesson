---
---

## Key functions in dplyr

| Function                                 | Returns                                                                                                               |
|------------------------------------------+-----------------------------------------------------------------------------------------------------------------------|
| filter(*data*, *conditions*)             | rows from *data* where *conditions* hold                                                                              |
| select(*data*, *variables*)              | a subset of the columns in *data*, as specified in *variables*                                                        |
| arrange(*data*, *variables*)             | *data* sorted by *variables*                                                                                          |
| group_by(*data*, *variables*)            | a copy of *data*, with groups defined by *variables*                                                                  |
| summarize(*data*, *newvar* = *function*) | a data frame with *newvar* columns that summarize *data* (or each group in *data*) based on an aggregation *function* |
| mutate(*data*, *newvar* = *function*)    | a data frame with *newvar* columns defined by a *function* of existing columns                                        |

<aside class="notes">

The table above presents the most commonly used functions in `dplyr`, which we will demonstrate in turn, starting from the *surveys* data frame.

</aside>

<!--split-->

## Subsetting and sorting

After loading dplyr, we begin our analysis by extracting the survey observations for the first three months of 1990 with `filter`:


~~~r
library(dplyr)
surveys_1990_winter <- filter(surveys,
			      year == 1990,
			      month %in% 1:3)
~~~
{:.text-document title="lesson-2.R"}

~~~
Error in filter_(.data, .dots = lazyeval::lazy_dots(...)): object 'surveys' not found
~~~
NA


~~~r
str(surveys_1990_winter)
~~~
{:.input}

~~~
Error in str(surveys_1990_winter): object 'surveys_1990_winter' not found
~~~
{:.output}

<aside class="notes">

Note that a logical "and" is implied when conditions are separated by commas. (This is perhaps the main way in which `filter` differs from the base R `subset` function.) Therefore, the example above is equivalent to `filter(surveys, year == 1990 & month %in% 1:3)`. A logical "or" must be specified explicitly with the `|` operator.

</aside>

<!--split-->

To choose particular columns (rather than the rows) of a data frame, we would call `select` with the name of the variables to retain.


~~~r
select(surveys_1990_winter,
       record_id, month, day, plot_id,
       species_id, sex, hindfoot_length, weight)
~~~
{:.input}

<!--split-->


Alternatively, we can *exclude* a column by preceding its name with a minus sign. We use this option here to remove the redundant year column from *surveys_1990_winter*:


~~~r
surveys_1990_winter <- select(surveys_1990_winter, -year)
~~~
{:.text-document title="lesson-2.R"}

~~~
Error in select_(.data, .dots = lazyeval::lazy_dots(...)): object 'surveys_1990_winter' not found
~~~
NA


~~~r
str(surveys_1990_winter)
~~~
{:.input}

~~~
Error in str(surveys_1990_winter): object 'surveys_1990_winter' not found
~~~
{:.output}

<!--split-->


To complete this section, we sort the 1990 winter surveys data by descending order of species name, then by ascending order of weight. Note that `arrange` assumes ascending order unless the variable name is enclosed by `desc()`.


~~~r
sorted <- arrange(surveys_1990_winter,
                  desc(species_id), weight)
~~~
{:.text-document title="lesson-2.R"}

~~~
Error in arrange_(.data, .dots = lazyeval::lazy_dots(...)): object 'surveys_1990_winter' not found
~~~
NA


~~~r
head(sorted)
~~~
{:.input}

~~~
Error in head(sorted): object 'sorted' not found
~~~
{:.output}

<!--split-->

### Exercise 2

Write code that returns the *record_id*, *sex* and *weight* of all surveyed individuals of *Reithrodontomys montanus* (RO).

<aside class="notes">

[View solution](#solution-2)

</aside>

<!--split-->

## Grouping and aggregation

Another common type of operation on tabular data involves the aggregation of records according to specific grouping variables. In particular, let's say we want to count the number of individuals by species observed in the winter of 1990.

<aside class="notes">

We first define a grouping of our *surveys_1990_winter* data frame with `group_by`, then call `summarize` to aggregate values in each group using a given function (here, the built-in function `n()` to count the rows).

</aside>


~~~r
surveys_1990_winter_gb <- group_by(surveys_1990_winter, species_id)
~~~
{:.text-document title="lesson-2.R"}

~~~
Error in group_by_(.data, .dots = lazyeval::lazy_dots(...), add = add): object 'surveys_1990_winter' not found
~~~
{:.text-document title="lesson-2.R"}

~~~r
counts_1990_winter <- summarize(surveys_1990_winter_gb, count = n())
~~~
{:.text-document title="lesson-2.R"}

~~~
Error in summarise_(.data, .dots = lazyeval::lazy_dots(...)): object 'surveys_1990_winter_gb' not found
~~~
NA


~~~r
head(counts_1990_winter)
~~~
{:.input}

~~~
Error in head(counts_1990_winter): object 'counts_1990_winter' not found
~~~
{:.output}

<!--split-->

A few notes on these functions: 

- `group_by` makes no changes to the data frame values, but it adds metadata -- in the form of R *attributes* -- to identify groups.
- You can add multiple variables (separated by commas) in `group_by`; each distinct combination of values across these columns defines a different group.
- A single call to `summarize` can define more than one variable, each with its own function.

<aside class="notes">

You can see attributes either by running the `str()` function on the data frame or by inspecting it in the RStudio *Environment* pane.

</aside>

<!--split-->


### Exercise 3

Write code that returns the average weight and hindfoot length of *Dipodomys merriami* (DM) individuals observed in each month (irrespective of the year). Make sure to exclude *NA* values.

<aside class="notes">

[View solution](#solution-3)

</aside>

<!--split-->

## Transformation of variables

The `mutate` function creates new columns by performing the same operation on each row. Here, we use the previously obtained *count* variable to derive the proportion of individuals represented by each species, and assign the result to a new *prop* column.


~~~r
prop_1990_winter <- mutate(counts_1990_winter,
                           prop = count / sum(count))
~~~
{:.text-document title="lesson-2.R"}

~~~
Error in mutate_(.data, .dots = lazyeval::lazy_dots(...)): object 'counts_1990_winter' not found
~~~
NA


~~~r
head(prop_1990_winter)
~~~
{:.input}

~~~
Error in head(prop_1990_winter): object 'prop_1990_winter' not found
~~~
{:.output}

<!--split-->

A few notes about transformations:

- With `mutate`, you can assign the result of an expression to an existing column name to overwrite that column.
- As we will see below, `mutate` also works with groups. The key difference between `mutate` and `summarize` is that the former always returns a data frame with the same number of rows, while the latter reduces the number of rows.
- For a concise way to apply the same transformation to multiple columns, check the `mutate_each` function. There is also a `summarize_each` function to perform the same aggregation operation on multiple columns.
^

<!--split-->

### Exercise 4

We often use `group_by` along with `summarize`, but you can also apply `filter` and `mutate` operations on groups.

- Filter a grouped data frame to return only rows showing the records from *surveys_1990_winter* with the minimum weight for each *species_id*.
- For each species in *surveys_1990_winter_gb*, create a new colum giving the rank order (within that species!) of hindfoot length. (Hint: Read the documentation under `?ranking`.)

<aside class="notes">

[View solution](#solution-4)

</aside>