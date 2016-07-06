### When 1M Records were inserted in batches of 200k each into MongoDB

##### 1. File Name : fwjr_prod.json, File Size : 5kB

| Record Count | Time to Write to DB
| -----------  |:-------------------:|
| 200k         | 48s 321ms           | 
| 400k         | 51s 916ms           | 
| 600k         | 53s 35ms            |
| 800k         | 47s 566ms           |
| 1M           | 47s 583ms           |

| Query  | Indexed (Y/N) | Execution Time (Millis)  | Total Docs Examined | Input Stage
| -----  |:-------------:| ------------------------:| -------------------------: | ------------------: |
| db.production.find({"wmaid":500}).explain()  | N | 2927 | 1000000 | COLLSCAN |
| db.production.find({"wmaid":500}).explain() | Y | 11 | 1 | IXSCAN |


##### Total Time : 4m 8s 421ms
##### Time taken for individual inserts = 10m 54s 871ms

------------------------

##### 2. File Name : main_doc.json, File Size : 12kB

| Record Count | Time to Write to DB
| -----------  |:-------------------:|
| 200k         | 1m 18s 313ms        | 
| 400k         | 1m 19s 957ms        | 
| 600k         | 1m 23s 583ms        |
| 800k         | 1m 17s 431ms        |
| 1M           | 1m 15s 937ms        |

| Query  | Indexed (Y/N) | Execution Time (Millis)  | Total Docs Examined | Input Stage
| -----  |:-------------:| ------------------------:| -------------------------: | ------------------: |
| db.production.find({"wmaid":500}).explain()  | N | 5730 | 1158003 | COLLSCAN |
| db.production.find({"wmaid":500}).explain()  | Y | 2    | 1       | IXSCAN   |

##### Total Time : 6m 35s 221ms
##### Time Taken for individual inserts = 12m 0s 014ms

### Index Vs Non Index Queries

##### File Name : main_doc.json, File Size : 12kB

| Query  | Indexed (Y/N) | Execution Time (Millis)  | Number of Results returned | Total Docs Examined | Total Keys Examined
| -----  |:-------------:| ------------------------:| -------------------------: | ------------------: | ------------------: |
| db.production.find({'PFNArray':{'$regex':'^root://test.ch/Run214/'}}).explain("executionStats")  | Y | 19601 | 3 | 1158000 | 3474000 |
| db.production.find({'PFNArray':{'$regex':'^root://test.ch/Run214/'}}).explain("executionStats")  | N |  |  |  |  |
| db.production.find({"LFNArray":{"$regex":"^/store/mc/Run727/"}}).explain() | Y | 6 | 3 | 3 | 13 |
| db.production.find({"LFNArray":{"$regex":"^/store/mc/Run727/"}}).explain() | N |  |  |  |  |
| db.production.find({"$or":[{"PFNArray": { "$regex" : "^root://test.ch/Run430/"} }, { "LFNArray": { "$regex" : "^/store/mc/Run121/"} }]}).explain("executionStats")  | Y | 27715 | 6 | 1158006 | 3474013 |
| db.production.find({"$or":[{"PFNArray": { "$regex" : "^root://test.ch/Run430/"} }, { "LFNArray": { "$regex" : "^/store/mc/Run121/"} }]}).explain("executionStats")  | N |  | |  |  |
| db.production.find({"steps.performance.storage.writeTotalMB": {"$gte":200,"$lte":250}}).explain() | Y | 115977 | 669535 | 1158000 | 3473800 |
| db.production.find({"steps.performance.storage.writeTotalMB": {"$gte":200,"$lte":250}}).explain() | N |  |  |  |  |
