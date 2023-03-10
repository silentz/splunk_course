## `appendpipe` command

Syntax: `... | appendpipe [<subpipeline>]`

* TLDR: append a table to the bottom of original table
* Transforms results and appends output to end of result set
* The `subpipeline` is executed when Splunk reaches the `appendpipe` command
	* Contains one or more transforming commands
	* Does not overwrite original results; instead, appends output as a new lines to the bottom of yje original results set
	* Multiple `appendpipes` can exist in a search

```
index=network sourcetype=cisco
| stats count by usage, username
| appendpipe [stats sum(count) by usage]
```

## `eventstats` command

Syntax: ```
```
...
| eventstats <statsfunction1>(<field1>) [as <new_field1>].
			 <statsfunction2>(<field2>) [as <new_field2>],
			 ...
```

* TLDR: Appends a single calculated value to each event
* Generates statistics of all existing fiels in your search results and saves theam as values in new fields
* `<statsfunction>` is applied to `<field>` and the resulting value is appended to each of the results

```
index=web action=remove
| stats sum(price) as lost_revenue by product_name
| eventstats avg(lost_revenue) as average
```

## `streamstats` command

Syntax:
```
...
| streamstats <statsfunction1>(<field1>) [as <new_field1>] [by <field_list1>] window=<int> current=<bool>, ...
```

* TLDR: calculate cumulative sum/avg/... and append as new column
* Calculates statistics for each resukt row at the time the command encounters it and adds these values to results
* The following options are available:
	* `window` specifies the number of events to use; default=0 (all events)
	* `current` includes the current event in summary calculations
		* default behavior: `current=true`
		* if set to `current=false`, then the search uses the field value from previous result

Example: calculate average cost of an item over last 24 hours (averaging over 100 last events)
```
index=web action=purchase status=200 productId=123
| table _time, price
| sort _time
| stramstats avg(price) as averagePrice window=100 current=false
```

## `fillnull` command

Syntax: `... | fillnull [value=<string>] [<field-list>]`

* Replace null values in fields
* Specify what to replace a null value with using `value=<string>`
	* if not specified, defaults to `value=0`
* Restrict which fields to apply `fillnull` to using `<field-list>`

```
index=web
| fillnull value=unknown username
```

## `bin` command

Syntax: `... | bin span=<interval> <field>`

* Split output results to bins with `<interval>` interval

```
index=sales price=*
| bin span=1000 price
```

## `xyseries` command

Syntax: `... | xyseries <x_field>, <y_field>, <y_data>`

* Reearange table to show as a timeseries (see example for better understanding)

Example: imagine we have table like this:
```
_time | host | data_transfered
------------------------------
  123 | www1 | 10000
  234 | www2 | 1100
  345 | www3 | 111111
  456 | www1 | 11232
  567 | www3 | 11000
  678 | www1 | 1000000000
  ... | ...  | ...
```
and we would like to see a plot with `_time` as X-axis, `data_transfered` as `Y-axis` and line plots for each host (`www1`, `www1`, `www2`). For this we will use `xyseries` command:

```
...
| table _time, host, data_transfered
| xyseries _time, host, data_transfered
```

## `untable` command

Syntax: `... | untable <col_name>, <row_name>, <new_col_name>`

* Transform table to a series (see example for better understaning)

Example: imagine we have table like this:
```
 product | Germany | France | Spain
------------------------------------
   apple |    1.5€ |   2.6€ |  1.7€
  orange |    2.5€ |   3.4€ |  2.2€
  banana |    0.5€ |   0.7€ |  1.1€
```

By using `untable product, country, price` we will transform table to series, where product, country and result will be items in table. The result would be:

```
 product | country | price 
---------------------------
   apple | Germany | 1.5€
   apple |  France | 2.6€
   apple |   Spain | 1.7€
  orange | Germany | 2.5€
  orange |  France | 3.4€
  orange |   Spain | 2.2€
  banana | Germany | 0.5€
  banana |  France | 0.7€
  banana |   Spain | 1.1€
```

## `chart` command

Generally, `chart` is used to display a chartable output.
```
...
| chart a over b by c
```
is equivalent to
```
...
| stats a by b, c
...
| xyseries b c a
```

## `foreach` command

Syntax: `... | foreach <column_list> [eval ...]`

Example 1: total field will be a sum of all hosts errors, which names start with `www` or `myhost`:
```
index=web status>=500
| chart count by action, host
| eval total=0
| foreach www*, myhost*
	[eval total=total+'<<FIELD>>']
```

Example 2: do the operation for each field of wildcard `www*`:
```
index=web
| timechart span=1h sum(byets) as totalBytes bu host
| foreach www*
	[eval <<FIELD>> = round(<<FIELD>> / (1024 * 1024), 2)]
```