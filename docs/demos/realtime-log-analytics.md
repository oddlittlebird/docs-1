# Realtime Log Analytics

Building real-time log analytics solution using GDN.

**GDN:** https://gdn.paas.macrometa.io/ 

| **Tenant** | **Passsword** | **Geo Fabric** |**Log Publisher** | **Dashboard**|**Source Code**|
|------------|---------- |-------------- |-------------- |------------|----------|
| demo@macrometa.io | `xxxxxxxx` | _system | [Log Publisher](https://macrometacorp.github.io/demo-realtime-log-analytics) | [Analytics Dashboard](https://dashboards.poc.macrometa.io/d/tWcKbZ8Mz/demo-realtime-log-analytics?orgId=1&from=1608387240000&to=1608440040000)| [github](https://github.com/Macrometacorp/demo-realtime-log-analytics)|


Sample Log File: [http_access.log](https://raw.githubusercontent.com/pzombade/mm-log-publisher/gh-pages/server.log)

## Solution

![Realtime Log Analytics](images/realtime-log-analytics.png)

**Collections:**

* http_verb_agg_count (doc collection)
* http_code_agg_count (doc collection)
* http_error_msgs (doc collection)

**Stream Workers:**

* log_processor
* agg_code_processor
* agg_verb_processor

**Search:**

* c8search_view_http_error_msgs
