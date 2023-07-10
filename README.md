<p align="center">
  <img src="https://dist.neo4j.com/wp-content/uploads/20210423072428/neo4j-logo-2020-1.svg" height='100' />
</p>

In this module we will learn about what graph databases are and what is neo4j. How they are better when compared with RDBMS databases.
We will also look at advantages and disadvantages of Graph Databases.

# What is a Graph Database?

A Neo4j graph database stores nodes and relationships instead of tables or documents.Your data is stored without restricting it to a pre-defined model, 
allowing a very flexible way of thinking about and using it.

### The property graph model

<img src="https://neo4j.com/docs/getting-started/_images/sample-cypher.svg"/>
<p align="center">
  <a href="https://neo4j.com/docs/getting-started/get-started-with-neo4j/graph-database/" target="_blank">Image Source Neo4j</a>
</p>


In Neo4j, information is organized as nodes, relationships, and properties.
  - **Nodes** are the entities in the graph.
    - Nodes can be tagged with labels, representing their different roles in your domain (for example, Person).
    - Nodes can hold any number of key-value pairs, or properties (for example, name).
    - Node labels may also attach metadata (such as index or constraint information) to certain nodes.
  - **Relationships** provide directed, named connections between two node entities (for example, Person LOVES Person).
    - Relationships always have a direction, a type, a start node, and an end node, and they can have properties, just like nodes.
    - Nodes can have any number or type of relationships without sacrificing performance.
    - Although relationships are always directed, they can be navigated efficiently in any direction.

# What is Neo4j?

Neo4j is a native graph database, which means that it implements a true graph model all the way down to the storage level.
Beyond the core graph, Neo4j also provides: ACID transactions, cluster support, and runtime failover.
  - Neo4j is offered as a managed cloud service via AuraDB (you can also run Neo4j yourself with either Community Edition or Enterprise Edition)
  - Neo4j is written in Java and Scala.
  - Neo4j uses Cypher, a declarative query language similar to SQL, but optimized for graphs.
  - Constant time traversals in big graphs for both depth and breadth due to efficient representation of nodes and relationships.
  - Enables scale-up to billions of nodes on moderate hardware.
  - Flexible property graph schema that can adapt over time, making it possible to materialize and add new relationships later to
    shortcut and speed up the domain data when the business needs change.
  - Drivers for popular programming languages, including Java, JavaScript, .NET, Python, and many more.

# Why graph databases?

existing relational databases can store these relationships, they navigate them with expensive JOIN operations or cross-lookups, often tied to a rigid schema. 
It turns out that "relational" databases handle relationships poorly. In a graph database, there are no JOINs or lookups. 
Relationships are stored natively alongside the data elements (the nodes) in a much more flexible format. 
Everything about the system is optimized for traversing through data quickly; millions of connections per second, per core.

Graph databases address big challenges many of us tackle daily. 
Modern data problems often involve many-to-many relationships with heterogeneous data that sets up needs to:

Navigate deep hierarchies.
Find hidden connections between distant items.
Discover inter-relationships between items.

# Where and how is Neo4j used?

<img src="https://neo4j.com/docs/getting-started/_images/use-case-summary.svg"/>
<p align="center">
  <a href="https://neo4j.com/docs/getting-started/get-started-with-neo4j/graph-database/" target="_blank">Image Source Neo4j</a>
</p>

    
