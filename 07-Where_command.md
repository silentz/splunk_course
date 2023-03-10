## `where` command

Syntax: `| where <eval expression>`

* Acts as a filter on search results by removing results taht do not match the `<eval-expression>`
	* Uses mathematical and boolean operators to evaluate values in the expression and return TRUE or FALSE
	* The `where` commands only returns results that evaluate to TRUE.
* Interprets unqouted or single-quoted strings as fields and double quoted strings as field values
* Treats field values in case-sensitive manner

```
index=web
| timechart count(eval(action="changedquantity")) as changes,
			count(eval(action="remove")) as removals
| where removals > changes
```

#### `where` command: wildcards

* Specify a wildcard by using the `LIKE` operator or the `like` function
	* `... | where <string> LIKE <pattern>`
	* `... | where like(<string>, <pattern>)`
* Do no use `*` as a wildcard, the `where` command will interpret this as a iteral chracter or mathematical operatror
* Use `%` for multilple characters or `_` for a single character

#### `where` command: `isnull` and `isnotnull`

Example with `isnull`:
```
index events
| where isnull(username)
```

Example with `isnotnull`:
```
index events
| where isnotnull(username)
```