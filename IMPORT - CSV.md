# Importing [CSV data](https://neo4j.com/docs/getting-started/data-import/csv-import/) into Neo4j

This article demonstrates different approaches to importing CSV data into Neo4j and provides solutions to potential issues that might arise during the process.

### What is CSV File

CSV is a file of comma-separated values, often viewed in Excel or some other spreadsheet tool.There can be other types of values as the delimiter, 
but the most standard is the comma. 

### Ways to import CSV files

There are a few different approaches to get CSV data into Neo4j based on the dataset size, as well as your degree of comfort with various tools.

Way| Description |
--- |--- |
LOAD CSV Cyphe|Camel case, beginning with an upper-case character |:VehicleOwner rather than :vehicle_owner        
neo4j-admin database import |Upper case, using underscore to separate words |:OWNS_VEHICLE rather than :ownsVehicle        
Neo4j ETL tool |Lower camel case, beginning with a lower-case character|firstName rather than first_name
Kettle import tool| 


### LOAD CSV command with Cypher

The LOAD CSV clause is a part of the Cypher query language. LOAD CSV is not simply a basic data ingestion mechanism. It performs multiple actions 
in a single operation:
- Supports loading/ingesting CSV data from a URI.
- Handles data conversion.
- Directly maps input data into complex graph/domain structures.
- Supports complex computations.
- Creates or merges entities, relationships, and structures.

### Reading CSV files

LOAD CSV can handle local and remote files. Local files may be loaded using a file:/// prefix before the file name. Since AuraDB is cloud based, 
this local file approach does not work with AuraDB.

Due to security reasons, local files, by default, can only be read from the Neo4j import directory, which is different based on your operating system. 
It is recommended to put files in Neo4j’s import directory, as it keeps the environment secure. 
```
//Example 1 - file directly placed in import directory (import/data.csv)
LOAD CSV FROM "file:///data.csv"

//Example 2 - file placed in subdirectory within import directory (import/northwind/customers.csv)
LOAD CSV FROM "file:///northwind/customers.csv"
```

Web-hosted files can be referenced directly with their URL, like https://host/path/data.csv. To read files from your local file system you need to 
check that the configuration setting dbms.security.allow_csv_import_from_file_urls is set to true
```
//Example 1 - website
LOAD CSV FROM 'https://data.neo4j.com/northwind/customers.csv'

//Example 2 - Google
LOAD CSV WITH HEADERS FROM 'https://docs.google.com/spreadsheets/d/<yourFilePath>/export?format=csv'
```
The default location for CSV files for import is the import directory for your Neo4j instance.

### Important tips for LOAD CSV

- All data from the CSV file is read as a string, so you need to use toInteger(), toFloat(), split(), or similar functions to convert values.
- Check your Cypher import statement for typos. Labels, property names, relationship types, and variables are case-sensitive.
- The cleaner the data, the easier the load. Try to handle complex cleanup/manipulation before load.

First, remember that Neo4j does not store null values. Null or empty fields in a CSV files can be skipped or replaced with default values in LOAD CSV.
```
//skip null values
LOAD CSV WITH HEADERS FROM 'file:///companies.csv' AS row
WITH row WHERE row.Id IS NOT NULL
MERGE (c:Company {companyId: row.Id});

// clear data
MATCH (n:Company) DELETE n;

//set default for null values
LOAD CSV WITH HEADERS FROM 'file:///companies.csv' AS row
MERGE (c:Company {companyId: row.Id, hqLocation: coalesce(row.Location, "Unknown")})

// clear data
MATCH (n:Company) DELETE n;

//change empty strings to null values (not stored)
LOAD CSV WITH HEADERS FROM 'file:///companies.csv' AS row
MERGE (c:Company {companyId: row.Id})
SET c.emailAddress = CASE trim(row.Email) WHEN "" THEN null ELSE row.Email END
```

### Optimizing LOAD CSV for performance

To improve inserting or updating unique entities into your graph (using MERGE or MATCH with updates), you can create indexes and constraints declared for 
each of the labels and properties you plan to merge or match on.

For best performance, always MATCH and MERGE on a single label with the indexed primary-key property. 
Suppose you use the preceding companies.csv file, and now you have a file that contains people and which companies they work for:
```
people.csv

employeeId,Name,Company
1,Bob Smith,1
2,Joe Jones,3
3,Susan Scott,2
4,Karen White,1

companies.csv

Id,Name,Location,Email,BusinessType
1,Neo4j,San Mateo,contact@neo4j.com,P
2,AAA,,info@aaa.com,
3,BBB,Chicago,,G
```
instead of the following:
```
MERGE (e:Employee {employeeId: row.employeeId})
MERGE (c:Company {companyId: row.companyId})
MERGE (e)-[r:WORKS_FOR]->(c)
```
You can write it like this:

```
// clear data
MATCH (n)
DETACH DELETE n;
// load Employee nodes
LOAD CSV WITH HEADERS FROM 'file:///people.csv' AS row
MERGE (e:Employee {employeeId: row.employeeId, name: row.Name})
RETURN count(e);
// load Company nodes
LOAD CSV WITH HEADERS FROM 'file:///companies.csv' AS row
WITH row WHERE row.Id IS NOT NULL
WITH row,
(CASE row.BusinessType
 WHEN 'P' THEN 'Public'
 WHEN 'R' THEN 'Private'
 WHEN 'G' THEN 'Government'
 ELSE 'Other' END) AS type
MERGE (c:Company {companyId: row.Id, hqLocation: coalesce(row.Location, "Unknown")})
SET c.emailAddress = CASE trim(row.Email) WHEN "" THEN null ELSE row.Email END
SET c.businessType = type
RETURN count(c);
// create relationships
LOAD CSV WITH HEADERS FROM 'file:///people.csv' AS row
MATCH (e:Employee {employeeId: row.employeeId})
MATCH (c:Company {companyId: row.Company})
MERGE (e)-[:WORKS_FOR]->(c)
RETURN *;
```
This way, the load is only doing one piece of the import at a time and can move through large amounts of data quickly and efficiently, reducing heavy processing.

When the amount of data being loaded is too much to fit into memory
- Batch the import into sections with CALL { …​ } IN TRANSACTIONS.
  ```
  LOAD CSV FROM 'file:///people.csv' AS line
  CALL {
   WITH line
   MATCH (e:Employee {id: line[0]})
   CREATE (e)-[:REL {prop: line[1]}]->(e)
  } IN TRANSACTIONS OF 100000 ROWS;
```
- Avoid the Eager operator - you can run PROFILE on your queries to see if they use Eager loading and either modify queries or run
multiple passes on the same file, so it does not do this.
- Adjust configuration for the database on heap and memory to avoid page-faults.Usually, you can create or update 1 million records in a single transaction
per 2 GB of heap. In neo4j.conf:
  - server.memory.heap.initial_size and server.memory.heap.max_size: set to at least 4G.
  - server.memory.pagecache.size: ideally, value large enough to keep the whole database in memory.

Please restart the neo4j instance for new configuration to take effect.
