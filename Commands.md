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

includes fields in result
```
index=web status IN ("500", "503")
| fields status
```
 will only print `status` field

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