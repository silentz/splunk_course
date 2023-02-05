### `chart` command

```
...
| chart <stats-func>(<field>) over <row-split>
		[by <column-split>] [span=<int><timescale>]
		[limit=<int>] [useother=<bool>] [usenull=<bool>]
```

* Rerurns results in a table format that can be displayed as a visualization
* Specify the Y-axis with `<stats-func>(<field>)`
	* `<field>` is a field with numeric values
	* `<stats-func>` is a statistical function:
		* `avg(value)`, `count(value)`, `distinct_count(value)`
		* `estdc(value)`, `estdc_error(value)`
		* `perc(value, perc)`, `exactperc(value, perc)`, `upperperc(value)`
		* `max(value)`, `min(value)`
		* `mean(value)`, `median(value)`, `mode(value)`
		* `stdev(value)`, `stdevp(value)`
		* `sum(value)`, `sumsq(value)`
		* `var(value)`, `varp(value)`
* Specify X-axis with `over <row-split>` and create single-siries data series
* Further split data by including `by <column-split>` and create a multi-series data series:
	* splits results of `<stats-func>(<field>)`
	* may alter the range of the Y-axis
* Control behaviour with `span`, `limit`, `useother`, `usenull`
* Alternative syntax: **by** clause with two arguments:
   `| chart <stats-func>(<field>) by <row-split> <column-split>`

### `timechart` command

```
...
| timechart <stats-function>(<field>) by <split-by-field>
			[span=<int><timescale>] [limit=<int>]
```

* Performs stats aggregations against time and returns a time series chart where `_time` is always the X-axis
* `<stats-func>(<field>)` applies a function to a single field and populates the Y-axis
	* if using the `count` function, a field does not need to be specified
* Use `by <split-by-field>` to further split results of the statistical aggregation
	* only one `<split-by-field>` can be specified
	* each distinct value of `<split-by-field>` will become a series
* Further control timechart behaviour with `span` and `limit` options

### `top` command
```
...
| top (<field>|<field-list>) [by <field>]
	   [countfield=<string>] [limit=<int>] [showperc=<bool>]
```

* Finds the most common values for `<field>` or `<field0list>`
* By default, outputs top 10 results in table format
* Group results with a `by <field>` clause
* Control behavior with `countfield`, `limit` and `showperc` options

### `rare` command

is the opposite of `top` command
has the same values

### `stats` command

```
...
| stats <stats-function>(<field>)
		[as <new-field-name>] [by <field-list>]
```

* Uses various functions to calculate statistics on search results
* Returns a results table containing the output of `<stats-function>` on `<field>`
* Group results with a `by <field-list>` clause
* Can output multiple stats at once:
  ```
  ...
  | stats min(value) as "Minimum",
		  max(value) as "Maximum",
		  avg(value) as "Average"
	by uage
   ```
   * Can be used with `values(value)` (stats unique values) and `list(value)` (stats all values) aggregate functions

### `eval` command

```
...
| eval <field1>=<expression1>[, <field2>=<expression2>]...
```
* Calculates an expression and puts the resulting value into a new field or overwrites an existing field
* Fields created by `eval` can be reused in the search pipeline
* Extremely useful command that supports a vast assortment of functions for performing specific tasks
* Can exist as a nested function in certain scenatios
* Supports various operators:
	* Arithmetic: `+`, `-`, `**`, `/`, `%`
	* Concatenation: `+`, '.'
	* Boolean: `AND`, `OR`, `NOT`, `XOR`
	* Comparison: `>`, `<`, `>=`, `<=`, `!=`, `=`, `LIKE`

#### `eval` functions

* `round(X, Y)` rounds X to Y decimal places, otherwise returns X as a whole number
* `pow(X, Y)` returns X to the power of Y
* `max(X1, X2, ..., Xn)` returns max value of Xi
* `min(X1, X2, ..., Xn)` returns min value of Xi
* `random()` takes no arguments and returns a random integer (from 0 to 2^31 - 1)

### `eval` command as a function

* Nest within the `stats count` function to count events with a specific field value
* Field values are case sensetive and should be wrapped in double quotes
* Requires an `as` clause to rename the field

Example:
```
...
| stats count(eval(action="Accepted")) as Accepted,
		count(eval(action="Failed")) as Failed,
		count(eval(action="session_opened")) as SessionOpened
```

### `rename` command

```
...
| rename <field1> as <newField1>,
		 <field2> as <newField2>,
		 ...,
		 <fieldN> as <newFieldN>
```

Rename fields in search. Does not keep old field names in search context. We can also use wildcards (`product*` (like regex)) in field names.

### `sort` command

```
...
| sort (+|-) [+|-]<field1> [+|-]<field2> ...
			 [limit=<int> | <int>]
```

* Sorts in ascending order by default
* Limit results returned with the `limit` option or just include a number
* Determines field type then determines sorting type:
	* Alplabetic strings = lexicographic sort
	* Numeric = numerical sort
	* Combination = lexicographic or numeric sort based on first character