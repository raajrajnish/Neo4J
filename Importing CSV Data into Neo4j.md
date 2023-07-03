**Preparing for Importing Data into Neo4j**
  - Steps for importing data into Neo4j.
  - Understanding the Neo4j data model.
  - Understanding the data to be used for the import.
  - Inspecting the data to make sure it is clean.

When you import data into Neo4j, you typically have a set of source files that were obtained from:
  - RDBMS
  - Web APIs
  - Public data directories
  - BI tools
  - Excel

Cypher has a built-in clause, LOAD CSV for importing CSV files.  If you have a JSON or XML file, you must use the APOC library 
to import the data, but you can also import CSV with APOC.

The types of data that you can store as properties in Neo4j include:
  - String
  - Long (integer values)
  - Double (decimal values)
  - Boolean
  - Date/Datetime
  - Point (spatial)
  - StringArray (comma-separated list of strings)
  - LongArray (comma-separated list of integer values)
  - DoubleArray (comma-separated list of decimal values)

There are two ways that you can import CSV data into Neo4j that you will learn in this course:
  - 1. Using the Neo4j Data Importer.
  - 2. Writing Cypher code to perform the import.

**Requirements for importing CSV data**
  - You must have one or more CSV files that represent the nodes and relationships that will be created in the graph.
  - You must also have an existing Neo4j DBMS that is started.

**Steps for preparing for importing data**
  - Understand the data in the source CSV files.
  - Inspect and clean (if necessary) the data in the source data files.
  - Create or understand the graph data model you will be implementing during the import.

# Understanding the Source Data

CSV file structure - A CSV file represents rows of data . When you are given CSV files, you must determine:
  - Whether the CSV file will have header information, describing the names of the fields.
  - What the delimiter will be for the fields in each row.

Including headers in the CSV file reduces syncing issues and is a recommended Neo4j best practice. Comma ',' is the default that Cypher uses in CSV file. 
If the source CSV files use a different field terminator, you must specify the FIELDTERMINATOR in your Cypher LOAD CSV clause.

**Normalized data** 
Data normalization is common in relational models. This enables you to have CSV files that correspond to a relational table where an 
ID is used to identify the relationships.

**De-normalized data**
With de-normalized data, the data is represented by multiple rows corresponding to the same entity, which will be loaded as a node. 
de-normalized data typically represents data from multiple tables in the RDBMS. 

**IDs must be unique**
When you load data from CSV files, you rely heavily upon the IDs specified in the file. 
use an ID as a unique property value for each node.If the IDs in your CSV file are not unique for the same entity (node), 
you will have problems when you load the data and try to create relationships between existing nodes.


# Inspecting the Data for Import

