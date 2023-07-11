<p align="center">
  <img src="https://dist.neo4j.com/wp-content/uploads/20210423072428/neo4j-logo-2020-1.svg" height='100' />
</p>

In this module we will learn about what graph databases are and what is neo4j. How they are better when compared with RDBMS databases.
We will also look at advantages and disadvantages of Graph Databases.

Please note all the diagrams and resources either created by author or directly taken form the Neo4j [website](https://neo4j.com/)

# What is a Graph Database?

A Neo4j graph database stores nodes and relationships instead of tables or documents.Your data is stored without restricting it to a pre-defined model, 
allowing a very flexible way of thinking about and using it.

### The property graph model

<img src="https://neo4j.com/docs/getting-started/_images/sample-cypher.svg" width='800'/>

In Neo4j, information is organized as nodes, relationships, and properties.

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

# Graph database concepts

Neo4j uses a property graph database model. 
A graph data structure consists of nodes (discrete objects) that can be connected by relationships. Below is the image of a graph with three nodes (the circles) and three relationships (the arrows).

<p align="center">
  <img src="https://neo4j.com/docs/getting-started/_images/graph_concept_three_nodes-arr.svg" height='250' width='250'/>
</p>

<p align="center">
  <a href="" target="_blank">Concept of a graph structure</a>
</p>

The Neo4j property graph database model consists of:

**Nodes** are used to represent entities (discrete objects) of a domain.
    - Nodes can be tagged with labels, representing their different roles in your domain (for example, Person).
    - Nodes can hold any number of key-value pairs, or properties (for example, name).
    - Node labels may also attach metadata (such as index or constraint information) to certain nodes.
    - Nodes can have zero or more labels to define (classify) what kind of nodes they are.

The simplest possible graph is a single node with no relationships. Consider the following graph, consisting of a single node.
	
 <p align="center">
  <img src="https://neo4j.com/docs/getting-started/_images/graph_single_node-arr.svg" height='250' width='250'/>
</p>

The node labels are:
  - Person
  - Actor
The properties are:
  - name: Tom Hanks
  - born: 1956

**Node labels**

Labels shape the domain by grouping (classifying) nodes into sets where all nodes with a certain label belong to the same set. For example, all nodes representing users could be labeled with the label User

Since labels can be added and removed during runtime, they can also be used to mark temporary states for nodes. A node can have zero to many labels. In the example graph, the node labels, Person, Actor, and Movie, are used to describe (classify) the nodes. More labels can be added to express different dimensions of the data.

<p align="center">
  <img src="https://neo4j.com/docs/getting-started/_images/graphdb-simple-labels-multi-arr.svg"/>
</p>


**Relationship** provide directed, named connections between two node entities (for example, Person LOVES Person).
    - Relationships always have a direction, a type, a start node, and an end node, and they can have properties, just like nodes.
    - Nodes can have any number or type of relationships without sacrificing performance.
    - Although relationships are always directed, they can be navigated efficiently in any direction.
    - Relationships must have a type (one type) to define (classify) what type of relationship they are. 
    - Nodes and relationships can have properties (key-value pairs), which further describe them.
    - It is possible for a node to have a relationship to itself.

<p align="center">
  <img src="https://neo4j.com/docs/getting-started/_images/graph_example_relationship-arr.svg"/>
</p>

The relationship type: ACTED_IN
The properties are:
- roles: ['Forrest']
- performance: 5

Relationships always have a direction. However, the direction can be disregarded where it is not useful. This means that there is no need to add duplicate relationships in the opposite direction unless it is needed to describe the data model properly.

Relationship type
A relationship must have exactly one relationship type.

<p align="center">
  <img src="https://neo4j.com/docs/getting-started/_images/graphdb-nodes-and-rel-arr.svg"/>
</p>

### Properties

Properties are key-value pairs that are used for storing data on nodes and relationships.The value part of a property:
- Can hold different data types, such as number, string, or boolean.
- Can hold a homogeneous list (array) containing, for example, strings, numbers, or boolean values.

### Traversals and paths

A traversal is how you query a graph in order to find answers to questions.Traversing a graph means visiting nodes by following relationships according to some rules. In most cases only a subset of the graph is visited.

Example - To find out which movies Tom Hanks acted in according to the tiny example database, the traversal would start from the Tom Hanks node, follow any ACTED_IN relationships connected to the node, and end up with the Movie node Forrest Gump as the result (see the black lines):

<p align="center">
  <img src="https://neo4j.com/docs/getting-started/_images/graphdb-traversal-arr.svg"/>
</p>

The shortest possible path has length zero. It contains a single node and no relationships. A path containing only a single node has the length of 0.
A path containing one relationship has the length of 1.

### Schema

A schema in Neo4j refers to indexes and constraints.Neo4j is often described as schema optional, meaning that it is not necessary to create indexes and constraints. You can create data — nodes, relationships and properties — without defining a schema up front. Indexes and constraints can be introduced when desired, in order to gain performance or modeling benefits.

### Indexes

Indexes are used to increase performance. To see examples of how to work with indexes, see Using indexes. For detailed descriptions of how to work with indexes in Cypher, see Cypher Manual → Indexes.

### Constraints
Constraints are used to make sure that the data adheres to the rules of the domain. "If a node has a label of Actor and a property of name, then the value of name must be unique among all nodes that have the Actor label".

### Naming conventions

Node labels, relationship types, and properties (the key part) are case sensitive, meaning, for example, that the property name is different from the property Name.The following naming conventions are recommended:

Graph entity| Recommended style |Example |
--- |--- | ---|
Node label|Camel case, beginning with an upper-case character |:VehicleOwner rather than :vehicle_owner        
Relationship type |Upper case, using underscore to separate words |:OWNS_VEHICLE rather than :ownsVehicle        
Property |Lower camel case, beginning with a lower-case character|firstName rather than first_name            
