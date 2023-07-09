<img src="https://dist.neo4j.com/wp-content/uploads/20210423072428/neo4j-logo-2020-1.svg" width="150" />

## Python Driver - The [Official Neo4j Driver](https://neo4j.com/docs/api/python-driver/current/api.html#api-documentation) for Python
In this module we will look on how to use the official driver to connect with Neo4j using python language. Upon completion of this module, you will have acquired the skills necessary to:
   * :large_blue_circle: Install the Python Driver for Neo4j.
   * :large_blue_circle: Construct a Connection String.
   * :large_blue_circle: Instantiate the Driver.
   * :large_blue_circle: Confirm the successful establishment of a connection between the Driver and Neo4j.

### Section 1 : The [Driver](https://neo4j.com/developer/python/)
Neo4j offers drivers that enable you to establish a connection to the database and build applications that perform operations such as creating, reading, updating, and deleting data within the graph. To execute a Cypher statement against a Neo4j database you will use an object called a Driver.
  - Driver version :point_right: **5.10**
  - Neo4j versions supported :point_right: **>= Neo4j 4.4**
  - Python versions supported :point_right: **>= Python 3.7**

:flashlight: _In your application, it is recommended to instantiate a single instance of the Driver per Neo4j cluster or DBMS, which can then be shared throughout your application._

**Installing the [Driver](https://pypi.org/project/neo4j/)**
```
pip install neo4j
```
**Creating a Driver Instance**

Every instance of the driver will establish a connection to either a DBMS or a Neo4j cluster, based on the value specified in the connection string. General syntax as below
```

driver = GraphDatabase.driver(
  connectionString,
  auth=(username, password),
  **configuration
)
```
**NEO4J_URI**- A connection string typically consists of four elements:
  - The scheme used to connect to the Neo4j instance - for example neo4j or neo4j+s (**required**)
  - The initial server address for the Neo4j DBMS - for example localhost or dbhash.databases.neo4j.io (**required**)
  - The port number that the DBMS is running on (**required if the instance is not running on the default port of 7687**)
  - Additional connection configuration (for example the routing context)

<img src="https://github.com/raajrajnish/Neo4J/blob/main/neo4j.png" alt="python driver" />

<p align="center">
  <a href="https://graphacademy.neo4j.com/" target="_blank">Image Source Graph Academy</a>
</p>


**1. Choosing your Scheme**

<img src="https://github.com/raajrajnish/Neo4J/blob/main/schema_1.png" alt="python driver" />

Variations of the bolt scheme can be used to connect directly to a single DBMS (within a clustered environment or standalone). This can be useful if you have a single server configured for data science or analytics.

<img src="https://github.com/raajrajnish/Neo4J/blob/main/schema_2.png" alt="python driver" />

**Which schema string is right for you?** - If you are connecting to a DBMS hosted on Neo4j Aura, you will always use the neo4j+s scheme. 
Additional connection information can be appended to the connection string after a ?.

**2. An [Authentication Token](https://neo4j.com/docs/operations-manual/current/authentication-authorization/)** - You can pass the username and password as a tuple to the auth parameter.
```
auth = (username, password)
```
**3. Additional [Driver Configuration](https://neo4j.com/docs/python-manual/current/) (Optional)** - This object allows you to provide advanced configuration options, for example setting the connection pool size or changing timeout limits.
```
GraphDatabase.driver(uri, auth=auth,
    max_connection_lifetime=30 * 60,
    max_connection_pool_size=50,
    connection_acquisition_timeout=2 * 60)
```
**Example - Establish a connection**
```
from neo4j import GraphDatabase

# Create an instance of the driver
driver = GraphDatabase.driver(os.getenv('NEO4J_URI'), auth=(os.getenv('NEO4J_USERNAME'), os.getenv('NEO4J_PASSWORD')))
```

**Verifying [Connectivity](https://neo4j.com/docs/api/python-driver/current/api.html#neo4j.Driver.verify_connectivity)**

This verifies if the driver can establish a reading connection to a remote server or a cluster. Some data will be exchanged. Raises:
Exception – if the driver cannot connect to the remote. Use the exception to further understand the cause of the connectivity problem.
Return type: None

```
# Create an instance of the driver
driver = GraphDatabase.driver(os.getenv('NEO4J_URI'), auth=(os.getenv('NEO4J_USERNAME'), os.getenv('NEO4J_PASSWORD')))
# Verify the connection details
driver.verify_connectivity()
```

### Section 2 : Interacting with Neo4j

You will learn how to:
  - Open a Session and execute a Unit of Work within a Transaction.
  - Execute Read and Write queries through the Driver.
  - Consume the results returned from Neo4j.
  - Handle potential errors thrown by the Driver.
  
**Sessions and Transactions** 

**Sessions**
Through the Driver, we open Sessions.It is important to remember that sessions are not the same as database connections.When the Driver connects to the database, it opens up multiple TCP connections that can be borrowed by the session. A query may be sent over multiple connections, and results may be received by the driver over multiple connections.

Instead, sessions should be considered a client-side abstraction for grouping units of work, which also handle the underlying connections. The connections themselves are managed internally by the driver and are not directly exposed to the application.

To open a new session, call the session() method on the driver.
```
with driver.session(database="people") as session:
```
If no database is supplied, the default database will be used. This is configured in the dbms.default_database in neo4j.conf, the default value is neo4j
You cannot create multiple databases in Neo4j Aura or in Neo4j Community Edition.

URL - https://neo4j.com/developer/manage-multiple-databases/

The default access mode is set to write, but this can be overwritten by explicitly calling the execute_read() or execute_write() functions.

**Transactions**

ACID TRANSACTIONS
A transaction, by definition, must be atomic, consistent, isolated, and durable. Many developers are familiar with ACID transactions from their work with relational databases, and as such the ACID consistency model has been the norm for some time.

Through a Session, we can run one or more Transactions.There are three types of transaction exposed by the driver:
  - Auto-commit Transactions
  - Read Transactions
  - Write Transactions

**Auto-commit Transactions**
are a single unit of work that are immediately executed against the DBMS and acknowledged immediately.
You can run an auto-commit transaction by calling the run() method on the session object, passing in a Cypher statement as a string and optionally an object containing a set of parameters.
```
session.run(
    "MATCH (p:Person {name: $name}) RETURN p", # Query
    name="Tom Hanks" # Named parameters referenced
)
```

FOR ONE-OFF QUERIES ONLY
In the event that there are any transient errors when running a query, the driver will not attempt to retry a query when using session.run(). For this reason, these should only be used for one-off queries and shouldn’t be used in production.

**Read Transactions**
When you intend to read data from Neo4j, you should execute a Read Transaction.
In a clustered environment (including Neo4j Aura), read queries are distributed across the cluster.
The session provides an execute_read() function, which expects a single parameter, a function that represents the unit of work.
```
# Define a Unit of work to run within a Transaction (`tx`)
def get_movies(tx, title):
    return tx.run("""
        MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
        WHERE m.title = $title // (1)
        RETURN p.name AS name
        LIMIT 10
    """, title=title)

# Execute get_movies within a Read Transaction
session.execute_read(get_movies,
    title="Arthur" # (2)
)
```

PARAMETERIZED QUERIES
In the query above, the the $ prefix of $title (1) indicates that this value relates to the parameter defined in the second argument (2) of the run() function call.

**Write Transactions**
If you intend to write data to the database, you should execute a Write Transaction.
In clustered environments, write queries are sent exclusively to the leader of the cluster. 
The leader of the cluster is then responsible for processing the query and synchronising the transaction across the followers and read-replica servers in the cluster.
```
# Call tx.run() to execute the query to create a Person node
def create_person(tx, name):
    return tx.run(
        "CREATE (p:Person {name: $name})",
        name=name
    )


# Execute the `create_person` "unit of work" within a write transaction
session.execute_write(create_person, name="Michael")
```

The following code defines a function that accepts a name parameter, then executes a write transaction to create a :Person node in the people database.
```
def create_person_work(tx, name):
    return tx.run("CREATE (p:Person {name: $name}) RETURN p",
        name=name).single()

def create_person(name):
    # Create a Session for the `people` database
    session = driver.session(database="people")

    # Create a node within a write transaction
    record = session.execute_write(create_person_work,
                                    name=name)

    # Get the `p` value from the first record
    person = record["p"]

    # Close the session
    session.close()

    # Return the property from the node
    return person["name"]
```

# Processing Results

Query results are typically consumed as a stream of records. The drivers provide a way to iterate through that stream.

**Result**
Here is an example query which retrieves a list of :Person nodes related to a given Movie.
```
# Unit of work
def get_actors(tx, movie): # (1)
    result = tx.run("""
        MATCH (p:Person)-[:ACTED_IN]->(:Movie {title: $title})
        RETURN p
    """, title=movie)

    # Access the `p` value from each record
    return [ record["p"] for record in result ]

# Open a Session
with driver.session() as session:
    # Run the unit of work within a Read Transaction
    actors = session.execute_read(get_actors, movie="The Green Mile") # (2)

    for record in actors:
        print(record["p"])

    session.close()
```

The above code can be broken down into two elements:
  - The get_actors() function defines a unit of work to be executed within a transaction, passed as the first argument of the function, 
    in this case referenced as tx
  - The execute_read() method executes the unit of work within a Read Transaction
  - The result of the execute_read() is a Result object.

The result object acts as a buffer for an iterable list of records and provides a number of options for accessing those records. Once a result is consumed, it is removed from the buffer.

Peeking at Results - If you wish to preview a result without consuming it, you can use the peek method.
```
# Check the first record without consuming it
peek = result.peek()
print(peek)
```
This can be used to preview the first record in the result without removing it from the buffer.

Keys - To get the keys for each record in the result, you can call the keys() method.
```
# Get all keys available in the result
print(result.keys()) # ["p", "roles"]
```

Single Result - If you only expect a single record, you can use the single() method on the result to return the first record.
```
def get_actors_single(tx, movie):
    result = tx.run("""
        MATCH (p:Person)-[:ACTED_IN]->(:Movie {title: $title})
        RETURN p
    """, title=movie)

    return result.single()
```
Value - If you wish to extract a single value from the remaining list of results, you can use the value() method.
```
def get_actors_values(tx, movie):
    result = tx.run("""
        MATCH (p:Person)-[r:ACTED_IN]->(m:Movie {title: $title})
        RETURN p.name AS name, m.title AS title, r.roles AS roles
    """, title=movie)

    return result.value("name", False)
    # Returns the `name` value, or False if unavailable
```
This method two parameters:
  - The key or index of the field to return for each remaining record, and returns a list of single values.
  - Optionally, you can provide a default value to be used if the value is None or unavailable.

Values - If you need to extract more than item from each record, you can use the values() method. The method expects one parameter per item requested from the RETURN statement of the query.
```
def get_actors_values(tx, movie):
    result = tx.run("""
        MATCH (p:Person)-[r:ACTED_IN]->(m:Movie {title: $title})
        RETURN p.name AS name, m.title AS title, r.roles AS roles
    """, title=movie)

    return result.values("name", "title", "roles")
```
In the above example, a list will be returned, with each entry containing values representing name, title, and roles.

Consume - The consume() method will consume the remainder of the results and return a Result Summary.
```
def get_actors_consume(tx, name):
    result = tx.run("""
        MERGE (p:Person {name: $name})
        RETURN p
    """, name=name)

    info = result.consume()
```
The Result Summary contains a information about the server, the query, execution times and a counters object which provide statistics about the query.
```
# The time it took for the server to have the result available. (milliseconds)
print(info.result_available_after)

# The time it took for the server to consume the result. (milliseconds)
print(info.result_consumed_after)
```
The counters object can be used to retrieve the number of nodes, relationships, properties or labels that were affected during a write transaction.
```
print("{0} nodes created".format(info.counters.nodes_created))
print("{0} properties set".format(info.counters.properties_set))
```
URL - https://neo4j.com/docs/api/python-driver/4.4/api.html#neo4j.ResultSummary

# Exploring Records

When accessing a record, either within a loop, list comprehension or within a single record, you can use the [] bracket syntax.
The following example extracts the p value from each record in the result buffer.
```
for record in result:
    print(record["p"]) # Person Node
```
You can also access a value using its index, as it relates to the value contained in the keys array:
```
# Get all keys available in the result
print(result.keys()) # ["p", "roles"]
```

# The Neo4j Type System

As Neo4j is written in Java (the j in Neo4j stands for Java after all), there are some discrepancies between the types stored in the Neo4j database and native Python types.

Some values like strings, floats, booleans, and nulls map directly to Python types, but more complex types need special handling.
Python Types to Neo4j Types

Serial| Python Type | Neo4j Cypher Type |
--- |--- | ---|
1 |**None** |null        
2 |**bool** |Boolean        
3 |**int**|Integer          
4 |**float**|Float          
5 |**str**|String          
6 |**bytearray**|Bytes [1]           
7 |**list**|List     
8 | **dict**|Map 
9 |**neo4j.spatial.Point**|Point          
10 |**neo4j.spatial.CartesianPoint**|Point (Cartesian)  
11 |**neo4j.spatial.WGS84Point**|Point (WGS-84)     
12 | **neo4j.graph.Node**|Node 
13 |**neo4j.graph.Relationship**|Relationship          
14 |**neo4j.graph.Path**|Path

**Nodes & Relationships**
Nodes and Relationships are both returned as similar classes.
```
result = tx.run("""
MATCH path = (person:Person)-[actedIn:ACTED_IN]->(movie:Movie {title: $title})
RETURN path, person, actedIn, movie
""", title=movie)
```
The query will return one record for each :Person and :Movie node with an :ACTED_IN relationship between them.
**Nodes**
We can retrieve the movie value from a record using the [] brackets method,
```
for record in result:
    node = record["movie"]
```
The value assigned to the node variable will be the instance of a Node. Node is a type provided by the Neo4j Python Driver to hold the information held in Neo4j for the node.
```
print(node.id)              # (1)
print(node.labels)          # (2)
print(node.items())         # (3)

# (4)
print(node["name"])
print(node.get("name", "N/A"))
```
  - The id property provides access to the node’s Internal ID
    eg. 1234
  - The labels property is a frozenset containing an array of labels attributed to the Node
    eg. ['Person', 'Actor']
  - The items() method provides access to the node’s properties as an iterable of all name-value pairs.
    eg. {name: 'Tom Hanks', tmdbId: '31' }
  - A single property can be retrieved by either using [] brackets or using the get() method. The get() method also allows you to define a default property if   none exists.

INTERNAL IDS
Internal IDs refer to the position in the Neo4j store files where the record is held. These numbers can be re-used, a best practice is to always look up a node by an indexed property rather than relying on an internal ID.

**Relationships**
Relationship objects are similar to a Node in that they provide the same method for accessing the internal ID and properties.
```
acted_in = record["actedIn"]

print(acted_in.id)         # (1)
print(acted_in.type)       # (2)
print(acted_in.items())    # (3)

# 4
print(acted_in["roles"])
print(acted_in.get("roles", "(Unknown)"))

print(acted_in.start_node) # (5)
print(acted_in.end_node)   # (6)
```
  - The id property holds the internal ID of the relationship.
    eg. 9876
  - The type property holds the relationship type
    eg. ACTED_IN
  - The items() method provides access to the relationships’s properties as an iterable of all name-value pairs.
    eg. {role: 'Woody'}
  - As with Nodes, you can access a single relationship property using brackets or the get() method.
  - start_node - an integer representing the internal ID for the node at the start of the relationship
  - end_node - an integer representing the internal ID for the node at the end of the relationship

**Paths**
If you return a path of nodes and relationships, they will be returned as an instance of a Path.
```
path = record["path"]

print(path.start_node)  # (1)
print(path.end_node)    # (2)
print(len(path))  # (1)
print(path.relationships)  # (1)
```
  - start_node - a Neo4j Integer representing the internal ID for the node at the start of the path
  - end_node - a Neo4j Integer representing the internal ID for the node at the end of the path
  - len(path) - A count of the number of relationships within the path
  - relationships - An array of Relationship objects within the path.

Path Segments
A path is split into segments representing each relationship in the path. For example, say we have a path of (p:Person)-[:ACTED_IN]→(m:Movie)-[:IN_GENRE]→(g:Genre), there would be two relationships.

  - (p:Person)-[:ACTED_IN]→(m:Movie)
  - (m:Movie)-[:IN_GENRE]→(g:Genre)

The relationships within a path can be iterated over using the iter() function.
```
for rel in iter(path):
    print(rel.type)
    print(rel.start_node)
    print(rel.end_node)
```
# Temporal Data Types

Temporal data types are implemented by the neo4j.time module.
It provides a set of types compliant with ISO-8601 and Cypher.
The table below shows the general mappings between Cypher and the temporal types provided by the driver.

Serial| Neo4j Cypher Type Type | Python driver type | Python built-in type|
--- |--- | ---| ---|
1 |**Date** |neo4j.time.Date|datetime.date        
2 |**Time** |neo4j.time.Time|datetime.time
3 |**LocalTime**|neo4j.time.Time|datetime.time          
4 |**DateTime**|neo4j.time.DateTime|datetime.datetime          
5 |**LocalDateTime**|neo4j.time.DateTime|datetime.datetime          
6 |**Duration**|neo4j.time.Duration|datetime.timedelta

```
# Create a DateTime instance using individual values
datetime = neo4j.time.DateTime(year, month, day, hour, minute, second, nanosecond)

#  Create a DateTime  a time stamp (seconds since unix epoch).
from_timestamp = neo4j.time.DateTime(1609459200000) # 2021-01-01

# Get the current date and time.
now = neo4j.time.DateTime.now()

print(now.year) # 2022
```

URL - https://neo4j.com/docs/api/python-driver/4.4/temporal_types.html

**Spatial Data Types**
Points

Serial| Cypher Type | Python Type |
--- |--- | ---|
1 |**Point** |neo4j.spatial.Point
2 |**Point (Cartesian)** |neo4j.spatial.CartesianPoint
3 |**Point (WGS-84)**|neo4j.spatial.WGS84Point       

**CartesianPoint***
A Cartesian Point can be created in Cypher by supplying x and y values to the point() function. The optional z value represents the height.
To create a Cartesian Point in Python, you can import the neo4j.spatial.CartesianPoint class.
```
# Using X and Y values
twoD=CartesianPoint((1.23, 4.56))
print(twoD.x, twoD.y)

# Using X, Y and Z
threeD=CartesianPoint((1.23, 4.56, 7.89))
print(threeD.x, threeD.y, threeD.z)
```
**WGS84Point**
A WGS84 Point can be created in Cypher by supplying latitude and longitude values to the point() function. To create a Cartesian Point in Python, you can import the neo4j.spatial.WGS84Point class.
```
london=WGS84Point((-0.118092, 51.509865))
print(london.longitude, london.latitude)

the_shard=WGS84Point((-0.086500, 51.504501, 310))
print(the_shard.longitude, the_shard.latitude, the_shard.height)
```
**Distance**
When using the distance() function in Cypher, the distance calculated between two points is returned as a float.
```
WITH point({x: 1, y:1}) AS one,
     point({x: 10, y: 10}) AS two

RETURN distance(one, two) // 12.727922061357855
```

# Handling Driver Errors

When executing a Cypher statement, certain exceptions and error cases may arise. One error could be a transient error that may be resolved if retried.
In the Neo4j Python Driver, an error extending the neo4j.exceptions.Neo4jError class will be thrown.

**Exception Types**

Depending on the nature of the error, you may receive one of the following exceptions:

  - neo4j.exceptions.Neo4jError - Raised when the Cypher engine returns an error to the client.
  - neo4j.exceptions.ClientError - The Client sent a bad request - changing the request might yield a successful outcome.
  - neo4j.exceptions.CypherSyntaxError - Raised when the Cypher statement contains one or more syntax errors
  - neo4j.exceptions.CypherTypeError - Raised when or more of the data types in the query is incorrect
  - neo4j.exceptions.ConstraintError - Raised when action is rejected due to a constraint violation
  - neo4j.exceptions.AuthError - Raised when authentication failure occurs.
  - neo4j.exceptions.Forbidden - Raised when the action is forbidden for the authenticated user
  - neo4j.exceptions.TransientError - The database cannot service the request right now, retrying later might yield a successful outcome
  - neo4j.exceptions.ForbiddenOnReadOnlyDatabase - The write cypher you are requesting cannot be run on a readonly database
  - neo4j.exceptions.NotALeader - The write query cannot be executed on the current server because it is not the leader of the cluster

You can catch the specific exception above within a try/catch block, or catch all Neo4jErrors instances:
```
# Import the Exception classes from neo4j.exceptions
from neo4j.exceptions import Neo4jError, ConstraintError

# Attempt a query
try:
    tx.run(cypher, params)
except ConstraintError as err:
    print("Handle constaint violation")
    print(err.code) # (1)
    print(err.message) # (2)
except Neo4jError as err:
    print("Handle generic Neo4j Error")
    print(err.code) # (1)
    print(err.message) # (2)
```
Exceptions contain code (1) and message (2) properties to help you further diagnose the problem.

**Error Codes**

The Neo4jError includes a code property, which provides higher-level information about the query.

Each status code follows the same format, and includes four parts:
```
Neo.[Classification].[Category].[Title]
(1)        (2)          (3)       (4)
```

  - Every Neo4j Error code is prefixed with Neo.
  - The Classification provides a high-level classification of the error - for example, a client-side error or an error with the database.
  - The Category provides a higher-level category for the error - for example, a problem with clustering, a procedure or the database schema.
  - The Title provides specific information about the error that has occurred.

URL - https://neo4j.com/docs/status-codes/current/
