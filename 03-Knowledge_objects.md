## List of Splunk knowledge objects

* Fields
* Fields extractions
* Field aliases
* Calculated fields
* Lookups
* Event types
* Tags
* Workflow actions
* Reports
* Alerts
* Macros
* Data models

## Field extractions

If data in specific index/sourcetype has a format not recognised by Splunk (not json, key-value, csv, ...), you can use field extractions to extract such fields.

Example: imageine data has the following format
```
host: www.test.com, username: admin, action: auth
```

What we see: obvious key-value pairs in logs
What Splunk sees by default: one text line
What Splunk will see after definig regex to parse this: the same as we see

## Field aliases

Assign alternate name to existing field in data.

Example: you have 3 different indexes, each of them has its own `username` entity, that are referensing same values in database, but in logs of each application they are written different:
__index a:__ `user=admin`
__index b:__ `username=admin`
__index c:__ `assignee=admin`

To normalize this, field aliases can be used to assign same name of entity in different logs.

## Calculated fields

Perform calculations based on the values of existing fields.
Create using `eval` command.

## Lookups

Additional fields and values, which does not contain in the data.

#### Types
1. File lookups - loaded from a `.csv` file
2. Lookups as binary executables or Python scripts
3. KV Store - access key-value pairs from key-value collections
4. KMZ files - geospacial lookups

## Event types

alias for a piece of a splunk search command

1. Create a search, for example:
```
index=web status=200 action=purchase
```
2. Save as -> Event Type -> Name=web_sales

Now we can save time and just type:
```
eventtype=web_sales
```
instead of `index=web status=200 action=purchase`

## Tags

Tags are used to assign names to specific field and value combinations. These fields can be event type, host, source, or sourcetype, etc. You can also use a tag to group a set of field values together, so that you can search for them with one command.

Create: Event -> Field -> Actioons -> Edit Tags

Use: in search use syntax `tag={{tag_name}}` or `tag::{{field_name}}={{tag_name}}` if tag names intersect on different values

Where `tag_name` is the name of the tag, and `field_name` is the original field key of the tag.

## Workflow actions

Workflow actions rnable a wide variety of interactions between indexed or extracted fields and other web resources. Workflow actions have a wide variety of applications. For example:

- Perform an external WHOIS lookup based on an IP address found in an event.   
- Use the field values in an HTTP error event to create a new entry in an external issue management system.
- Launch secondary searches that use one or more field values from selected events.
- Perform an external search (using Google or a similar web search application) on the value of a specific field found in an event.

Create: Settings -> Fields -> "New" on "Workflow Action"

## Macros

Similar to event times, also can be reused. But thay can contain variables and act like functions.

Example:
```
add_object(1) -> rename * as "$object$"
Arguments: object
```

Create: Settings -> Advanced search -> "Add new" for "Search macros"

Use: state name of macros in backtics
```
index=abc
| eval user="@" + username
| `add_object(abcdefg)`
```

## Data models

Hierarchically structured datasets. Consits of three types of datasets:
1. Events
2. Searches
3. Transactions

Allow users to explore data in a graphical interface without ever having to understand Splunk search language.