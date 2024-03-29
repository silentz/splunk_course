## Comments

* Use three backticks before and after your comments
* Comment out portions of your search to help identify and isolate problems
* To make very long SPL easier to read, add comments directly after pipe

Example:
```
index=security sourcetype=linux
| ```line chart with week-to-week comparison```
	timechart span=1d count as Failures
| timewrap 1w
| rename _time as Day
| eval Day = strftime(Day, "%A")
```


## Default extracted fields

* `_raw`  - plaintext event
* `_time` - timestamp
* `host` - host of the event
* `source` - name of the source. example: `/var/log/nginx/access.log`
* `sourcetype` - semantics of the source given by human expert. example: `access logs`

## `NOT` operator and `!=` operator

* `(index=web OR index=security) status!=200`
	* returns events where `status` is not 200
* `(index=web OR index=security) NOT status=200`
	* returns events where `status` is not 200 AND where is no `status` field at all

## `highlight` command

* Highlight text in search results
* `... | highlight 404 500`

## `IN` operator

Same results for these two searches:
* `index=web (status=200 OR status=300 or status=400)`
* `index=web status IN (200, 300, 400)`

## `fields` command

* includes or exclude fields in result.
* makes searches more efficient

Example 1: only print `status` field
```
index=web status IN ("500", "503")
| fields status
```

Example 2: remove `user_agent` field
```
index=web status IN ("500", "503")
| fields -user_agent
```

## `rename` command

* rename field in output table header

Example:
```
index=web status IN ("500", "503")
| rename status AS "HTTP Status"
```

## `eval` command

* create new fields from existing ones

Example:
```
index=net sourcetype=cisco
| stats sum(sc_bytes) as Bytes by usage
| eval mbps = Bytes/1024/1024
```

## `erex` command

* uses automatic logic to extract fields from raw text **by given examples** of values of this field
* works better when given more examples

Example:
```
index=games sourcetype=cube
| erex Character fromfield=_raw examples="pixie, cowboy"
```

## `rex` command

* uses regular expressions capture groups to extract values in search time
* can extract multiple fields at a time
* default extraction field (if not stated) will be `_raw`, so it is more correct to state source field as precise as possible to win in computing time

Example: extract `sql_table` field from events  `_raw` field
```
index=dblogs sourcetype=sql
| rex field=_raw "^select .* from (?<sql_table>.*)"
```

## `table` command

* similar to `fields` command, but it is transforming command, so it returns statistics insted of events (can be used in report)
* Syntax: `table field1 field2 field3`
* will return fields in given order

Example:
```
index=web
| table username ip
```

## `dedup` command

* Removes duplicated events, keeps only unique values of combination of given columns

Example: keep only unique pairs of (username, ip)
```
index=dblogs sourcetype=sql
| dedup username ip
```

## `addtotals` command

* Adds `Total` column with sum of all numeric columns in a row
* Also can be configured:
	* change sum axis from rows to cols
	* change col/row name (depends on the mode)

Example:
```
index=web
| stats count(bytes) by host
| addtotals
```

## `fieldformat` command

Syntax: `| fieldformat <field>=<eval-expression>`

* Changes the format of a field's values with an `<eval-expression>`
* Only changes the appearance, not the underlying value
* Accepts a wide variety of evaluation functions
* Should be used late in the pipeline beacuse formatted results cannot be modified by other commands

Example: will change `5430.23` to `$5,430.23` in *statistics view*
```
| fieldformat Total = "$" + tostring(Total, "commas")
```

## `top` command
```
...
| top (<field>|<field-list>) [by <field>]
	   [countfield=<string>] [limit=<int>] [showperc=<bool>]
```

* Finds the most common values for `<field>` or `<field0list>`
* By default, outputs top 10 results in table format
* Group results with a `by <field>` clause
* Control behavior with `countfield`, `limit` and `showperc` options

Example 1: get top 20 results of `vendor` and `product_name` cols combination
```
index=sales
| top vendor product_name limit=20
```

Example 2: show top 3 products for each vendor
```
| top product_name by Vendor limit=3
```

## `rare` command

* same as `top` command, but searches for the LEAST command

Example: get 20 rarest results of `vendor` and `product_name` cols combination
```
index=sales
| rare vendor product_name limit=20
```

## `iplocation` command

* Extracts city, country, region, latitude and longitude if available by given ip-address
```
index=security
| iplocation src_ip
```

## `geostats` command

* visualizes geo events on map
```
| geostats latfield=a longfield=b count by product_name
```

## `fieldsummary` command

Syntax:
```
| ...
| fieldsummary [maxvals=<int>] [field-list]
```

* Returns statistics table for each table in output
* `maxvals` specifies the maximum distinct values to return for each field. Cannot be negative. Set `maxvals = 0` to return all available distinct values for each field. Default `100`.
* For each field exports statistics, such as:
	* `count` - number of events that contain this field
	* `distinct_count` - number of unique values of this field
	* `is_exact` - wheather or not the `distinct_count` field is exact. If the number of values of the field exceeds `maxvals`, then `fieldsummary` will stop retaining all the values and compute an approximate distinct count instead of an exact one. 1 means it is exact, 0 means it is not.
	* `max` - maximum value of field (if field is numeric)
	* `min` - minimum value of field (if field is numeric)
	* `mean` - mean of field values (if field is numeric)
	* `stdev` - standart deviation of field values (if field is numeric)
	* `numeric_count` - number of numeric values of this field
	* `values` - distinct values and their counts as json

## `tstats` command

Syntax:
```
| tstats <stats-func> ...
```

* Returns information about data schema (indexes, sourcetypes, their fields, ...)

## Informational functions

* `typeof(<field>)` - returns a string that represents the data type of `field`. Possible results: `number`, `string`, `bool`

This functions return true/false depending on the type of `field` argument:
* `isbool(<field>)`
* `isint(<field>)`
* `isnull(<field>)`
* `isnotnull(<field>)`
* `isnum(<field>)`
* `isstr(<field>)`