## Makeresults command

Syntax:
```
| makeresults [count=<num>]
			  [<format>]
			  [data=<string>]
```

* Is used to generate temporary results in memory.
* It needs to be the very first command in search.
* `format` argument is always `csv` or `json`: specifies the format of the inline data supplied by the `data` argument.
* `count`: the number of results to generate, the results have only the `_time` field.
* `data`: a collection of inline data that `makeresults` converts into events. If you provide a `data` argument, `makeresults` expects this data to follow the format specified by a corresponding `format`.

Example 1: generate single event
```
| makeresults
| eval username="admin"
| ...
```

Example 2: generate multiple empy results
```
| makeresults count=5
```

Example 3: generate multiple events in json format
```
| makeresults format=json data="[{\"name\":\"John\", \"age\":35}, {\"name\":\"Sarah\", \"age\":39}]"
```

Example 4: generate multiple events in csv format
```
| makeresults format=csv data="name, age
John,35
Sarah,39"
```

Example 5: generate multiple events using item ids
```
| makeresults count=4
| streamstats id
| eval age = case(id=1, 25, id=2, 39, id=3, 31, id=4, null())
| eval city = case(id=1 OR id=3, "Paris", id=2 OR id=4, "Seattle")
```