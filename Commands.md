### Default extracted fields

* `_raw`  - plaintext event
* `_time` - timestamp
* `host` - host of the event
* `source` - name of the source. example: `/var/log/nginx/access.log`
* `sourcetype` - semantics of the source given by human expert. example: `access logs`

### `NOT` operator and `!=` operator

* (index=web OR index=security) status!=200
	* returns events where `status` is not 200
* (index=web OR index=security) NOT status=200
	* returns events where `status` is not 200 AND where is no `status` field at all!

These searches are completely different!

### `IN` operator

* index=web (status=200 OR status=300 or status=400)
* index=web status IN (200, 300, 400)

Same results for these two searches

### `fields` command

includes or exclude fields in result.
can make search more efficient
```
index=web status IN ("500", "503")
| fields status
```
will only print `status` field
```
index=web status IN ("500", "503")
| fields -user_agent
```
will remove `user_agent` field

### `rename` command

rename field in output table header
```
index=web status IN ("500", "503")
| rename status AS "HTTP Status"
```

### `eval` command

create new fields from existing ones
```
index=net sourcetype=cisco
| stats sum(sc_bytes) as Bytes by usage
| eval mbps = Bytes/1024/1024
```

### `erex` command

uses automatic logic to extract fields from raw text **by given examples** of values of this field
```
index=games sourcetype=cube
| erex Character fromfield=_raw examples="pixie, cowboy"
```
sometimes works bad
works better when given more examples

### `rex` command

uses regular expressions capture groups to extract values in search time. can extract multiple fields at a time

```
index=dblogs sourcetype=sql
| rex field=_raw "^select .* from (?<sql_table>.*)"
```
creates field `sql_table` in events extracted from `_raw` field.

default extraction field (if not stated) will be `_raw`, so it is more correct to state source field as precise as possible to win in computing time.

### `table` command

* similar to `fields` command, but it is transforming command, so it returns statistics insted of events (can be used in report)
* format: `table field1 field2 field3`

will return fields in given order

### `dedup` command

* Removes duplicated events, keeps only unique values of combination of given columns

```
index=dblogs sourcetype=sql
| dedup username ip
```
will keep only unique pairs of (username, ip)

### `addtotals` command

* Adds `Total` column with sum of all numeric columns in a row
* Also can be configured:
	* change sum axis from rows to cols
	* change col/row name (depends on the mode)

### `fieldformat` command

* Change the view of data without changing data in event (all changes only in UI)

```
| fieldformat Total = "$" + tostring(Total, "commas")
```
will change `5430.23` to `$5,430.23` in *statistics view*.

### `top` command

* Finds the most common values in the result field
```
index=sales
| top vendor product_name limit=20
```
will get top 20 results of `vendor` and `product_name` cols combination
// default limit is 10
// see more params in docs

* can be grouped with `by` clause by other column
```
| top product_name by Vendor limit=3
```
will show top 3 products for each vendor

### `rare` command

* same as `top` command, but searches for the LEAST command

### `stats` command

Stats aggregation functions:
* `count`
* `distinct count`
* `sum`
* `average`
* `min`
* `max`
* `list`
* `values`

```
| stats sum(price) by product_name, store_name
```

### `chart` command

```
| chart <a> over <b1>, <b2>
```
where `b1, b2, ...` is `X` axis and `a` is Y axis (numeric)
`a` can be aggregate function

// see other options in docs

### `timechart` command

* same as Chart, by X axis is always time, but `by` can be used to set field names of categorical column to present multiple lines on timechart

```
| timechart sum(price) by product_name
```

// `span` arg - time bucket (default = 24h)

### `iplocation` command

Extracts city, country, region, latitude and longitude if available by given ip-address
```
index=security
| iplocation src_ip
```

### `geostats` command

* visualizes geo events on map
```
| geostats latfield=a longfield=b count by product_name
```