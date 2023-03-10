## Evaluation functions examples

* `case(X, "Y", ...)` Cycles through expressions and returns first value (Y) where the expression (X) evaluates to TRUE
* `cidrmatch("X", Y)` returns TRUE or FALSE based on wheather an IP matches a CIDR notation
* `if(X, Y, Z)` if X evaluates to TRUE, returns Y, otherwise returns Z
* `like(<string>, <pattern>)` returns TRUE if `<string>` matches `<pattern>`
* `in(<field>, <value-list>)` return TRUE if a value in the `<value-list>` matches a value in `<field>`
* `match(SUBJECT, "<regex>"` Returns TRUE or FALSE based on wheather the SUBJECT matches the `<regex>`
* `true()` returns TRUE
* `false()` returns FALSE
* `null()` returns NULL
* `nullif(X, Y)` returns NULL if X=Y, else returns X
* `validate(X, Y, ...)` opposite of case function: returns Y for the first expression (X) that evaluates to FALSE
* `searchmatch(X)` return TRUE if the search string X matches the event

## Eval text functions

* `upper(X)` converts all letters of string `X` to uppercase
* `lower(X)` converts all letters of string `X` to lowercase
* `substr(X, from, count)` returns substring `X[from:from+count]` (indexes start with 1)
* `replace(X, from, to)` replaces **regex** `from` to string `to` in string `X`

## `coalesce` function

Syntax: `coalesce(X1, X2, ...)`

* `coalesce` returns first non-null value from fields `X1`, `X2`, ...

## `if` function

Syntax: `if(X, Y, Z)`

* If X evaluates to TRUE, returns Y, otherwise returns Z

```
...
| eval is_parent=if(children > 0, true, false)
```

## `case` function

Syntax: `case(X1, "Y1", X2, "Y2", ...)`

* Cycles through expressions and returns first value (Y) where the expression (X) evaluates to TRUE
* Returns NULL if no expression evaluates to TRUE

```
...
| eval level=case(score < 1000, "Beginner",
			 	  score < 10000, "Intermediate",
				  score >= 10000, "Advanced")
```

## `validate` function

Syntax: `validate(X1, "Y1", X2, "Y2", ...)`

* Cycles through expressions and returns first value (Y) where the expression (X) evaluates to FALSE
* Returns NULL if no expression evaluates to FALSE

```
...
| eval portWarn=validate(port > 0, "Port must be positive",
					 	 port >= 1 AND port <= 65535, "Port value is out of range")
```

## `in` function

Syntax: `in(<field>, <value-list>)`

* Returns TRUE if one of the values in `<value-list>` matches a value from `<field>`
* Must be used within the `if` function or `case` function with `eval`

```
...
| eval error=if(in(status, "404", "500", "503"), "true", "false")
```

## `searchmatch` function

Syntax: `eval <field> = if(searchmatch(X), Y, Z)`

* Returns TRUE if event matches the search string
* Must be used within the `if` functuion with eval

```
...
| eval result = if(searchmatch("auth"), "auth event", "non-auth event")
```

## `cidrmatch` function

Syntax: `cidrmatch("X", Y)`
* Returns TRUE/FALSE based in whether proveded IP-address (Y) matches subnet specified by X

```
...
| eval isLocal = if(cidrmatch("10.2.0.0/16", ip), "local", "non-local")
```

## `match` function

Syntax: `match(SUBJECT, "<regex>")`

* Returns TRUE/FALSE based in whether the SUBJECT matches `<regex>` pattern.

```
...
| eval isNumeric=if(match(src, "^(0|(\-?[1-9][0-9]*))$"), "true", "false")
```

## `tostring` function

Syntax: `... | eval <field> = tostring(X, Y)`

* Specify a numeric field for `X`
* Determine formatting of `X` by defining `Y`
	* `commas` formats `X` with commas
	* `hex` converts `X` to hexadecimal
	* `duration` converts `X` to `HH:MM:SS`

## `tonumber` function

Syntax: `... | eval <field> = tonumber(NUMSTR[, BASE])`

* Specify a field name or literal string value with `NUMSTR`
* Define the base of `NUMSTR` with `BASE` (optional)
	* Can be from 2 to 36
	* Defaults to 10