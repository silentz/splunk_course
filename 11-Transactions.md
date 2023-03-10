## `transaction` command

```
...
| transaction (<field>|<field-list>)
	[maxspan=<timeUnit>]
	[maxpause=<timeUnit>]
	[maxevents=<int>]
	[startswith=<searchString>]
	[endswith=<searchString>]
	[keepevicted=<bool>]
```

* TLDR: Groups multiple events into single event with multivalue fields
*  `transaction` requires events to be in reverse chronological order
* Groups events that share one or more fields
	* Group events on values from `<field>`
	* Group events based on shared values from `<field-list>`
* Determine event grouping behavior using options:
	* Ranges of time:
		* `maxspan` - control maximum total time between first and last transaction events, defaults to `-1` (no span limit)
		* `maxpause` - control maximum total time between individual events, defaults to `-1` (no max time limit)
	* Maximum number of events in a transaction: `maxevents`
	* Values contained in the first/last events: `startswith`, `endswith`
	* Keep or not non-closed transactions:
		* `keepevicted=1` - shows complete and incomplete transactions
		* `keepevicted=0` - shows complete transactions only
* Adds some fields to events:
	* `duration` - time difference in seconds between first and last event
	* `eventcount` - number of events in transaction
	* `closed_txn` -  field is set to `0`, or `false`, for evicted transactions and `1`, or `true` for non-evicted (closed) transactions (use with `keepevicted=1`)

Example 1: group all events with the same `JSESSIONID` in single event
```
index=web sourcetype=access
| transaction JSESSIONID
```

Example 2: group all events with same `JSONSESSIONID` in single event, where first action is `addtocart` and last is `purchase`:
```
index=web sourcetype=access
| transaction JSESSIONID
	startswith=(action=addtocart AND status=200)
	endswith=(action=purcahse AND status=200)
```

Example 3: see ratio of complete purchases
```
index=web sourcetype=access
| transaction JSESSIONID
	startswith=(action=addtocart AND status=200)
	endswith=(action=purcahse AND status=200)
	keepevicted=1
| search action=addtocart
| stats count(eval(closed_txn=1)) as "Total Completed",
		count as "Total Attempted"
| eval "Percent Completed" = 'Total Completed' / 'Total Attempted'
```

## `transaction` considereations

* The `transaction` command is resouce intensive
* `transaction` has a limit of 1000 events per transaction
* `stats` has no limit
* Only use `transaction` if you:
	* Need to group events on values from multiple fields
	* Need to define event grouping on start/end values or segment on time
	* Want to keep raw data associated with each event
* Otherwise use `stats` command
	* Fatser and moe efficient
	* Can perorm calculations
	* Can group events based on a single field value (e.g. `by src_ip`)