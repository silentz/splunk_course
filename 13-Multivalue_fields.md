fMultivalue functions and commands provide ways to count, evaludate, modify, create, combine, sort and split multivalue fields. Typically contain a `mv` prefix.

## `spath` command

Syntax:
```
| ...
| spath [input=<field>] [output=<field>] [(path=<datapath>)|<datapath>]
```

* Extract data from multivalue JSON / XML field.
* `input` - field to extract data from
* `output` - field to output extracted value
* `path=<datapath>` or `<datapath>` - location path within the multivalue field of the value we want to extract
* by default, extracts all field from first 5000 characters of `input`

Can be also used as function:
```
| ...
| eval username = spath(<input_field>, <datapath>)
```

## `multikv` command

Syntax:
```
| ...
| multikv [fields <f1> <f2> ... <fN>] [filter <i1> <i2> ... <iN>]
```

* Command to extract table-formatted data (like `ps aux` or `netstats` logs)
* Splits each line of table (in single event) to separate event in splunk (2 events with 10-lines-table in each will split to 20 events)
* `fields` options is used to extract only specified fields
* `filter` options is used to include only table rows that contain at list one field value from a specified list of fields

## `list` function

Syntax:
```
| ...
| stats list(X) [as <newfield> | by <field>]
```

* Lists all field values for a given field `X`, creating a Multivalue field
	* Listed fields can be renamed as `<newfield>`, otherwise the field name will be `list(X)`
	* Values can be gruped by `<field>`
	* If more than one value is listed, the result is a multivalue field
* The order of the value reflects the order of events

## `values` function

Syntax:
```
| ...
| stats values(X) [as <newfield> | by <field>]
```

* Syntax is identical to `list` function
* Unlike `list` function, the values function lists all _unique_ values of field `X`

## `split` function

Syntax:
```
| ...
| eval <field> = split(<mvfield>, "<delimiter>")
```

* Create multivalue field by splitting a single-value field based on a given delimiter
* `<mvfield>` - single-value input field
* `"<delimiter>"` - delimiter that  `<mvfield>` is split with

## `makemv` command

Syntax:
```
| ...
| makemv [delim=<string> | tokenizer=<regex>] <field>
```

* Converts existing single value firld to a multivalue field based on a provided string or regex:
	* `delim` specified a string delimiter (default is a single space " ")
	* `tokenizer` specified a regular expression

## `mvcount` function

Syntax:
```
| ...
| eval <field>=mvcount(<mvfield>)
```

* Returns number of values for a specific multivalue field `<mvfield>`
* Returns `NULL` when
	* The field has no values
	* If a field does not exist for a particular result

## `mvindex` function

Syntax:
```
| ...
| eval <field>=mvindex(<mvfield>, startIndex, [endIndex])
```

* Get a value from a multivalue field like it is an array.
* `<mvfield>` - field to extract value(s) from
* `startIndex` - if `endIndex` is not specified, that returns `mvfield[startIndex]` value, otherwise return `mvfield[startIndex:endIndex]`
* `endIndex` - see `startIndex`

## `mvfilter` function

Syntax:
```
| ...
| eval <field>=mvfilter(<booleanExpression>)
```

* Filters a multivalue field based on an arbitrary boolean expression
* `<booleanExpression>` can reference only one field at a time

Example (filter all users starting with `admin_`):
```
| index=systems sourcetype=system_info
| eval names=mvfilter(match(root_users, "^admin_.*$"))
| dedup names
| table names
```

## `mvsort` function

Syntax:
```
| ...
| eval <field>=mvsort(<mvfield>)
```

* Returns a multivalue field with values sorted lexicographically (using ascii codes)
* `<mvfield>` - multivalue input field

## `mvzip` function

Syntax:
```
| ...
| eval <field>=mvzip(X, Y, [<delimiter>])
```

* Concatenates the values of two multivalue fields with a delimiter
	* `X` and `Y` are multivalue fields
	* The first value of `X` is concatenated with the first value of `Y`, etc.
	* `<delimiter>` is used as the separator; if none specified, the default delimiter is a comma (`,`)
* Fields `X` and `Y` should have the same number of values
* Resulting field values are treated as strings

Example:
```
index=systems sourcetype=processes
| rename CPU_CORES{}.core as core,
		 CPU_CORES{}.core_percent_used as percent
| eval cpu_core_load = mvzip(core, percent, " : ")
| table pid, process_name, cpu_core_load
```

## `mvjoin` function

Syntax:
```
| ...
| eval <field>=mvjoin(<mvfield>, "<delimiter>")
```

* Concatenate all values of multivalue field into a single-value field with a specified delimiter.

Example:
```
| ```users here = [admin, root, nobody]```
| ...
| eval users = mvjoin(users, ", ")
| ```users here = "admin, root, nobody"```
```

## `mvmap` function

Syntax:
```
| ...
| eval <field>=mvmap(<mvfield>, <expression>)
```

* Performs an operation over the values of multivalue field then returns a multivalue field as a result

Example:
```
| ```data here = [2, 4, 8]```
| ...
| eval data = mvjoin(data, data / 2)
| ```users here = [1, 2, 4]```
```

## `mvappend` function

Syntax:
```
| ...
| eval <field>=mvappend(arg1, arg2, ..., argN)
```

* Takes two or more single-value fields, multivalue fields, and strings and returns a multivalue field as a result
* Each input becomes a separate value within the new multivalue field

Example:
```
| ```assume data = [1, 2, 3]```
| ...
| eval data = mvappend(data, "4", "5", username)
| ```data = [1, 2, 3, "4", "5", "admin"]```

```

## `mvexpand` command

Syntax:
```
| ...
| mvexpand <field> [limit=<integer>]
```

* Takes a multivalue field and creates a separate event for each value
* Related field values are copied from the original event
* `limit` determines the maximum number of new events created from a multivalue field

Example:
```
| makeresults
| eval a=1
| eval b=mvappend("2", "3")
| ```results in two events: (a=1, b=2) and (a=1, b=3)```
```
