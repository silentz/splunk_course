## Types of lookups

1. File lookups - loaded from a `.csv` file
2. Lookups as binary executables or Python scripts
3. KV Store - access key-value pairs from key-value collections
4. KMZ files - geospacial lookups

## `inputlookup` command

Syntax: `| inputlookup [<filename>|<lookup-definition>]`

* TLDR: prints lookup table contents
* Use `<filename>` for searching lookup `.csv` or `.csv.gz` files
* Use a lookup's `<lookup-definition>` name to search lookup tables configured for any lookup type
* Event-generationg command: should be first command in a search following a pipe character

## `lookup` command

Syntax: `... | lookup <lookup-table-name> <lookup-field>`

* The `<lookup-table-name>` can reference:
	* name of `.csv` file
	* lookup definition name associated with lookup table files
* The lookup's `<lookup-field>` is used to match against the events
* By default, Splunk adds all remaining fields in the lookup table to the events

Syntax: `... | lookup <lookup-table-name> <lookup-field> [OUTPUT|OUTPUTNEW (<lookup-destfield>)]`

* Youcan specify one or more `<lookup-destfield>` to be added to the events, also called "lookup output fields"
* Change overwrite behavior for existing fields that match the lookup output fields with modifiers:
	* `OUTPUT`: overwrite existing fields
	* `OUTPUTNEW` do not overwrite existing fields
* Lookup output fields only exist for the duration of the search

## `outputlookup` command

Syntax: `... | outputlookup <filename>|<lookup-definition> createinapp=BOOL`

* Write search results to a specific file-based lookup (CSV) or KV Store collection
* Can be executed from a search, ad-hoc report, scheduled search or alert
* `createinapp` option if set to `TRUE`, will output lookup only to directory of current app, `FALSE` will create lookup in system directory

## Adding subsearch

Syntax: `search ... [subsearch]`

* Subsearches are enclosed in square brackets
* Subsearches are always executed first, before passing the results to the ither search
* Resuls, returned by subsearch, will be added inplace to search:
	* origin: `search index=abc [inputlookup users]`
	* result: `search index=abc root test admin`
* If subsearch returns multiple columns, they will be added to search with `AND`, each line, returned by subsearch, will be added to search with `OR`:
	* origin: `search index=abc [search index=users | where id < 1]`
	* result: `search index=abc (user=root AND id=0) OR (user=admin AND id=1)`
* Use `... | format` command to what will subsearch be transformed to
* Subsearch are defaullt limited to 60 seconds and 10k results, if subsearch run to loong or returns too much, it will stop with these limits
* Use subsearch only if there is no other way to write a search

## `return` command

Syntax: `return [<count>] [<field>] [<alias>=<field>] [$<field>...]`

* TLDR: return values from subsearch to search
* `<count>` is an integer that tells Splunk how many rows of results to return; by default, Splunk only returns the first row
* Specify one or more `<field>` to return, separated by spaces
	* Use `$<field>` to return just the values (no field name)
	* Use `<alias>=<field>` to return and rename fields
* Return gives you more control over how many values and whether to return field name or not. If it is not neccecery for your subsearch, can be replaced by `fields` command

Example:
```
index=security
| stats count by src_ip
| where count > 10
| sort -count
| return 3 ip=src_ip
```