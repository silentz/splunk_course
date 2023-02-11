## Splunk User Roles

Roles determine what a user is able to see, do, and interact with. There are three main roles in Splunk Enterprise. 

#### Administrator role
* can install apps
* ingest data
* create knowledge objects for all users

#### Power user role
* create and share knowledge objects with all users of an app
*  do realtime searches

#### User role
* only see their own knowledge objects and those that have been shared with them

## Knowledge objects classification by its purpose

#### Data interpretation:
* fields
* custom field extractions
* calculated fields

#### Data classification:
* event types
* transactions: related events

#### Data normalization:
* tags
* field aliases

#### Data enrichment:
* lookups
* workflow actions

#### Data models:
* hierarchically structed datasets

## Naming conventions for knowledge objects

- **Group**: Corresponds to the working group(s) of the user saving the search.
- **Search type**: Indicates the type of search (alert, report, summary-index-populating).
- **Platform**: Corresponds to the platform subjected to the search.
- **Category**: Corresponds to the concern areas for the prevailing platforms.
- **Time interval**: The interval over which the search runs (or on which the search runs, if it is a scheduled search).
- **Description**: A meaningful description of the context and intent of the search, limited to one or two words if possible. Ensures the search name is unique.

**Possible reports using this naming convention:**
-   SEG_Alert_Windows_Eventlog_15m_Failures
-   SEG_Report_iSeries_Jobs_12hr_Failed_Batch
-   NOC_Summary_Network_Security_24hr_Top_src_ip

## Reports
* Create: Search -> Type the command -> Save as -> Report
* Accessable from "Search" app
* **Use naming conventions for naming reports**
* Reports can be scheduled in crontab-like style (runned on fixed time, for example on monday morning, to not be run each time the report is accessed)
* Reports can be shown only for user, for all users off apps, or in all aps (admin role required)
* Report execution finish can be chained with Splunk actions (email, webhook, ...)
* Report can be embedded into any webpage by clicking Edit -> Embed, and then pasting the result to an `iframe` in any html.
* Report can be run as Owner when viewed by anyone, or by user
* Reports can be saved as a chart only, as a table of events only, or both
* Can be added to a dashboard

## Dashboards
* Acessable from "Search" app
* Dasboard is a collections of reports, collected in one tab
* **Use naming conventions for naming dashboards**
* Create: Search -> Type command -> Save as -> Dashboard -> New
* Append: Search -> Type command -> Save as -> Dashboard -> Existing

## Alerts
* Can notify users with many different ways when results of a scheduled search meet required condition.
* Triggers when search is completed.
* They can (for example): list in interface, log event, output to lookup, send to endpoint, send email, ...
* Can be throttled for given period of time (trigger only once in time interval no matter how many times did alert trigger in this interval)
* Create: Search -> Save as -> Alert

#### Types of alerts
* Scheduled alert: run alert on a schedule
* Real-time: search is run continiously (starts next after last is finished)

## Alert actions
* Set of actions to perform when alert is triggered. Can be build-in (send email or webhook) or something custom (send to slack, telegram, ...).

## Searches
* Commands and raw text terms are not case sensetive! Values of commands are case sensetive!
* Boolean opeations order: **NOT**, **OR**, **AND**
* _Transforming commands_ - ones that give statistics instead of only modifying the event

#### Search modes
* **Fast**: Field discovery is disabled for this mode,  only returning information on default fields, and fields required to fulfill your search.
* ***Verboose**: this mode returns as much field and event data as possible, discovering all the fields it can.
* **Smart**: this is default mode, it will toggle behavior based on the type of search you are running.

## Field sidebar

#### Selected fields
* Default: host, source and sourcetype
* Can be also be added from event by clicling "Selected -> Yes"

#### Interesting fields
* Fields that happen at least in 20% of events
* `\alpha` letter symbol - text field
* `#` symbol - number field

## Entities evaluation order

1. Field extractions
2. Field aliases
3. Calculated field
4. Lookups
5. Event types
6. Tags

This means calculated field (eval) can reference field extractions value, but for example field aliases cannot reference field from lookups.