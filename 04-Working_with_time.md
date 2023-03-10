Time is stored in `_time` timestamp field. 

## Earliest and latest modiefiers

```
... earliest=[+|-]<timeInt><timeUnit>@<timeUnit>
... latest=[+|-]<timeInt><timeUnit>@<timeUnit>
```

* These options should be included in search to overrife the time range picker from UI.
* `<timeInt><timeUnit>` is the time amout expresses as an interger and a time unit, e.g. `3h` = 3 hours. Other time units:
	* current timestamp: `now`
	* second: `s`, `sec`, `secs`, `second`, `seconds`
	* minute: `m`, `min`, `mins`, `minute`, `minutes`
	* hour: `h`, `hr`, `hrs`, `hour`, `hours`
	* day: `d`, `day`, `days`
	* week: `w`, `week`, `weeks`
	* month: `mon`, `month`, `months`
	* quater: `q`, `qtr`, `qtrs`, `quater`, `quaters`
	* year: `y`, `yr`, `yrs`, `year`, `years`
* `@<timeUnit>` zeros everything after specified time unit:
	* Always rounds down, i.e. go backwards through time
	* Can be used to snap to a certain day of week: `@w0` for Sunday, `@w1` for Monday, etc.

Examples (now is 9:45 am of 1st April 2021):
* `-30m@h` looks back to 09:00:00 on April 1-st 2021
* `-h@h` rounds down to 08:00:00 on Aprtil 1-st 2021
* `earliest=-mon@mon latest=@mon` looks for events from 00:00:00 on March 1-st 2021 to 00:00:00 on April 1-st 2021
* `earliest=-7d@d` Looks for events from 00:00:00 on March 25th (7 days before April 1-st) to 09:45 on April 1-st 2021.
* `earliest=@d+3h` looks for events from 03:00:00 to 09:45:00 on April 1-st 2021.

## Default time fields

* events with timestamp information have `date_*` fields
* Tjese fields are generated for events that include date/timestamp in tje raw data
* Provides extra information for searching
* These fields do not change based in a user's time zone
* eg: `date_hour`, `date_mday`, `date_minute`, `date_month`, `date_second`, `date_wday`, `date_year`, `date_zone`

## `bin` command

```
...
| bin <timeField> [span=<int><timescale>] [as <newField>]
```

* when used with `timeField=_time`, `bin` can sort result values into discrete sets, or bins based on time
* time values are adjusted so that all items in a bin share the same time value
* ! Can also be used with non-time commands just to group numerical values in bins

## Formating time

* `eval x=now()` - returns the time a search was started
* `eval x=relative_time(X, Y)` - returns an epoch timestamp relative to a supplied time
	* `X` is a number, represening desired time in epoch seconds
	* `Y` is a relative time specifier
	* eg `eval yesterday = relative_time(now(), "-1d@h")`
* `strftime` and `strptime` -- functions to format timestamp to text format / parse text format to timestamp. Modifiers:
	* `%H` is 24 hour (`00-23`)
	* `%T` is 24 hour in `HMS` format
	* `%I` is 12 hour (`01-12`)
	* `%M` is minute (`00-59`)
	* `%p` is `AM` or `PM` (used with `%I`)
	* `%d` is day of month (`01-31`)
	* `%w` is weekday (`0-6`)
	* `%a` is abbrivated weekday (`Sun, Mon, Tue, ...`)
	* `%A` is weekday (`Sunday, Monday, Tuestday, ...`)
	* `%F` is year-month-day (same as `%Y-%m-%d`)
	* `%b` is abbrivated month name (`Jan`, `Feb`, ...)
	* `%B` is month name (`January, February`, ...)
	* `%m` is month number (`01-12`)
	* `%Y` is 4-digit year (eg `2023`)