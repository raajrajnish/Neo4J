# Neo4j Python Driver

You will learn about the lifecycle of the Neo4j Driver and how it should be used within your application. 
By the end of this module, you will have learned to how to:
  - Install the Neo4j Python Driver.
  - Build a Connection String.
  - Create an instance of the Driver.
  - Verify that the Driver has successfully connected to Neo4j.

# About the Driver

To execute a Cypher statement against a Neo4j database you will use an object called a Driver. To connect to and query Neo4j 
from within a Python application, you use the Neo4j Python Driver.
URL - https://neo4j.com/developer/python/

You should create a single instance of the Driver in your application per Neo4j cluster or DBMS, which can then be shared across your application.

**Installing the Driver**
```
pip install neo4j
```

**Creating a Driver Instance**

Each driver instance will connect to one DBMS, or Neo4j cluster, depending on the value provided in the connection string.
Here is an example for how to create a driver instance to connect to a Neo4j instance running on localhost on port 7687 with 
the username neo4j and password neo:
```
# Import the neo4j dependency
from neo4j import GraphDatabase

# Create a new Driver instance
driver = GraphDatabase.driver("neo4j://localhost:7687",
    auth=("neo4j", "neo"))
```

**Verifying Connectivity**
```
# Verify the connection details
driver.verify_connectivity()
```

**Connection Strings and Authentication**
Let’s take a closer look at the driver() function and how it is used to create a driver instance.

```
driver = GraphDatabase.driver(
  connectionString, // (1)
  auth=(username, password), // (2)
  **configuration // (3)
)
```

The neo4j.driver() function accepts the following arguments:
  - A connection string
  - An authentication method to use when connecting - in this case, username/password authenticatio
  - Optionally, you can provide additional configuration as named parameters

**1. The Connection String**- A connection string typically consists of four elements:
  - The scheme used to connect to the Neo4j instance - for example neo4j or neo4j+s (required)
  - The initial server address for the Neo4j DBMS - for example localhost or dbhash.databases.neo4j.io (required)
  - The port number that the DBMS is running on (required if the instance is not running on the default port of 7687)
  - Additional connection configuration (for example the routing context)
  - 
<p align="center">
  <img src="https://github.com/raajrajnish/Neo4J/blob/main/neo4j.png" alt="python driver" height=300/>
</p>


**Choosing your Scheme**
  - neo4j - Creates an unencrypted connection to the DBMS. If you are connecting to a local DBMS or have not explicitly turned on encryption then
    this is most likely the option you are looking for.
  - neo4j+s - Creates an encrypted connection to the DBMS. The driver will verify the authenticity of the certificate and fail to verify connectivity
    if there is a problem with the certificate.
  - neo4j+ssc - Creates an encrypted connection to the DBMS, but will not attempt to verify the authenticity of the certificate.

