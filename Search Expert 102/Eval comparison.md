### Evaluation functions examples

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

### `if` function

Syntax: `if(X, Y, Z)`

* If X evaluates to TRUE, returns Y, otherwise returns Z

```
...
| eval is_parent=if(children > 0, true, false)
```

### `case` function

Syntax: `case(X1, "Y1", X2, "Y2", ...)`

* Cycles through expressions and returns first value (Y) where the expression (X) evaluates to TRUE
* Returns NULL if no expression evaluates to TRUE

```
...
| eval level=case(score < 1000, "Beginner",
			 	  score < 10000, "Intermediate",
				  score >= 10000, "Advanced")
```

### `validate` function

Syntax: `validate(X1, "Y1", X2, "Y2", ...)`

* Cycles through expressions and returns first value (Y) where the expression (X) evaluates to FALSE
* Returns NULL if no expression evaluates to FALSE

```
...
| eval portWarn=validate(port > 0, "Port must be positive",
					 	 port >= 1 AND port <= 65535, "Port value is out of range")
```

### `in` function

Syntax: `in(<field>, <value-list>)`

* Returns TRUE if one of the values in `<value-list>` matches a value from `<field>`
* Must be used within the `if` function or `case` function with `eval`

```
...
| eval error=if(in(status, "404", "500", "503"), "true", "false")
```