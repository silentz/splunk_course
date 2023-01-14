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

### Field aliases

Assign alternate name to existing field in data.

### Calculated fields

Perform calculations based on the values of existing fields.

### Lookups

Additional fields and values, which does not contain in the data.

### Event types

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

### Tags

Tags are used to assign names to specific field and value combinations. These fields can be event type, host, source, or sourcetype, etc. You can also use a tag to group a set of field values together, so that you can search for them with one command.

Create: Event -> Field -> Actioons -> Edit Tags

Use: in search use syntax `tag={{tag_name}}` or `tag::{{field_name}}={{tag_name}}`

Where `tag_name` is the name of the tag, and `field_name` is the original field key of the tag.

### Workflow actions

Workflow actions rnable a wide variety of interactions between indexed or extracted fields and other web resources. Workflow actions have a wide variety of applications. For example:

- Perform an external WHOIS lookup based on an IP address found in an event.   
- Use the field values in an HTTP error event to create a new entry in an external issue management system.
- Launch secondary searches that use one or more field values from selected events.
- Perform an external search (using Google or a similar web search application) on the value of a specific field found in an event.

### Macros

Similar to event times, also can be reused. But thay can contain variables and act like functions.

Example:
```
add_object(1) -> rename * as "$object$"
Arguments: object
```

### Data models

Hierarchically structured datasets. Consits of three types of datasets:
1. Events
2. Searches
3. Transactions

Allow users to explore data in a graphical interface without ever having to understand Splunk search language.