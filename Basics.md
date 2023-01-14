### Roles

Roles determine what a user is able to see, do, and interact with. There are three main roles in Splunk Enterprise. 

Administrator role:
* can install apps
* ingest data
* create knowledge objects for all users

Power role:
* create and share knowledge objects with all users of an app
*  do realtime searches

User role:
* only see their own knowledge objects and those that have been shared with them

### Knowledge objects

5 categories:
* Data interpretation:
	* fields
	* custom field extractions
	* calculated fields
* Data classification:
	* event types
	* transactions: related events
* Data normalization:
	* tags
	* field aliases
* Data enrichment:
	* lookups
	* workflow actions
* Data models:
	* hierarchically structed datasets

### Reports

Accessable from "Search" app.
Create: Search -> Type the command -> Save as -> Report
! Set naming convention for organization

* Reports can be scheduled in crontab-like style (runned on fixed time, for example on monday morning, to not be run each time the report is accessed)
* Reports can be shown only for user, for all users off apps, or in all aps (admin role required)
* Report execution finish can be chained with Splunk actions (email, webhook, ...)
* Report can be embedded into any webpage by clicking Edit -> Embed, and then pasting the result to an `iframe` in any html.
* Report can be run as Owner when viewed by anyone, or by user
* Reports can be saved as a chart only, as a table of events only, or both
* Can be added to a dashboard

### Dashboards

Acessable from "Search" app.
Dasboard is a collections of reports, collected in one tab.

Create: Search -> Type command -> Save as -> Dashboard -> New
Append: Search -> Type command -> Save as -> Dashboard -> Existing
Edit: can reorganize dashboard

### Alerts

* Can notify users with many different ways when results of a scheduled search meet required condition.
* Triggers when search is completed.
* They can (for example): list in interface, log event, output to lookup, send to endpoint, send email, ...

Scheduled alert: run alert on a schedule
Real-time: search is run continiously (starts next after last is finished)

Can be throttled for given period of time (trigger only once in time interval no matter how many times did alert trigger in this interval)

Create:
1. Create a search
2. Save as -> Alert

! All triggered alerts can be seen in `Activity` -> `Triggered alerts`

### Alert actions

Set of actions to perform when alert is triggered. Can be build-in (send email or webhook) or something custom (send to slack, telegram,telegram, ...).

### Search

* Commands and raw text terms are not case sensetive! Values of commands are case sensetive!
* Boolean opeations order: **NOT**, **OR**, **AND**

Modes:
* **Fast**: Field discovery is disabled for this mode,  only returning information on default fields, and fields required to fulfill your search.
* ***Verboose**: this mode returns as much field and event data as possible, discovering all the fields it can.
* **Smart**: this is default mode, it will toggle behavior based on the type of search you are running.

### Field sidebar

Selected fields:
* Default: host, source and sourcetype
* Can be also be added from event by clicling "Selected -> Yes"

Interesting fields:
* Fields that happen at least in 20% of events
* alpha letter symbol - text field, # symbol - number field
* List of fields can be filtered in submenu

### Calculated fields

* This option behaves like `eval` command, that can be defined as a preconfigured option in splunk settings
* Each event that has required input fields will automatically be extended by splunk with calculated field
* Calculated fields can only referrence events that exist in raw event.

### Field aliases

* Allow to assign alternatre names to same semantic values.
```
agent -> username
user -> username
username -> username
```

### Evaluation order

Field extractions ->
Field aliases ->
Calculated field -> 
Lookups ->
Event types ->
Tags

This means calculated field (eval) can reference field extractions value, but for example field aliases cannot reference field from lookups.