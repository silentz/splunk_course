## `append` command

```
... | append [search ...]
```

* Append results of subsearch to the end of results of main search
* Does not work with real-time searches

Example: add info about login attampts of invalid usernames to info of login attempts of valid users
```
index=security "failed password" NOT "invalid user"
| timechart count as known_users
| append
	[search index=security "failed password" "invalid user"
	 | timechart count as unknown_users]
```

## `appendcols` command

```
... | appendcols [search ...]
```

* Append results of subsearch as new cols of the main search
* These columns are just appended to the result, without any matching to any other keys of main search

Example:
```
index=web
| appendcols [search index=linux]
```

## `join` command

* Combines result of search with the result of subsearch and joins them by at leat one **common field**
* Acts like join in SQL, can be inner/outer/left/...

Example: map ip addresses of one index events with usernames of other index events
```
index=security src_ip=10.0.0.0/8 "Failed"
| join src_ip
	[search index=vpn ip=*
	 | dedup username
	 | rename ip src_ip
	 | fields src_ip, username]
| table src_ip, username
```

## `union` command

// TODO: rewrite logic for streaming and non-streaming datamodels (multisearch and so on...)

* Union is a ganaration command (start swarch with it)
* Combines search results from 2 or more datasets into one:
	* savedsearches (using `savedsearch:` prefix)
	* lookups (using `lookup:` prefix)
	* datamodels (using `datamodel:` prefix)
	* subsearches

Example: combine results from datamodel with the results of subsearch
```
| union datamode;:Name_of_dataset
	[search index=sales sourcetype=vendor_sales]
| table sourcetype, product_name
```