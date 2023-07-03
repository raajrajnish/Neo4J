**Graph elements** Let’s take a closer look at the two elements that make up a graph:
  - Nodes (also known as vertices)
  - Relationships (also known as edges)

**Nodes**
Nodes (or vertices) are the circles in a graph. Nodes commonly represent objects, entities, or merely things.Nodes typically represent things
Examples of entities that could typically be represented as a node are:
  - 1. person,
    2. product,
    3. event,
    4. book or
    5. subway station.

**Relationships**
Relationships (or edges) are used to connect nodes. We can use relationships to describe how nodes are connected to each other.
Relationships are typically verbs. 
  - We could use a relationship to represent a personal or professional connection (Person knows Person, Person married to Person), 
  - to state a fact (Person lives in Location, Person owns Car, Person rated Movie), 
  - or even to represent a hierarchy (Parent parent of Child, Software depends on Library).

**Graph Structure**
1. Directed vs. undirected graphs - In an undirected graph, relationships are considered to be bi-directional or symmetric.If Michael is married to Sarah, then it stands to reason that Sarah is also married to Michael.A directed graph adds an additional dimension of information to the graph. Relationships with the same type but in opposing directions carry a different semantic meaning.For example, if marriage is a symmetrical relationship, then the concept of love is asymmetrical. Although two people may like or love each other, the amount that they do so may vary drastically. 
2. Weighted vs. unweighted graphs - In a weighted graph, the relationships between nodes carry a value that represents a variety of measures, for example cost, time, distance or priority. A basic shortest path algorithm would calculate the shortest distance between two nodes in the graph. This could be useful for finding the fastest walking route to the local store or working out the most efficient route to travel from city to city.

**Graph traversal** - How one answers questions about the data in a graph is typically implemented by traversing the graph. To find the shortest path between Springfield to Centerville. Neo4j’s Cypher statement language is optimized for node traversal so that relationships are not traversed multiple times, which is a huge performance win for an application.

**Graphs Are Everywhere** - Various Use cases
1. E-commerce and real-time recommendations - by adding a graph database, either as a primary data store or as an additional data store, we can start to serve real time recommendations. Example url - https://neo4j.com/graphgists/northwind-recommendation-engine/
2. Investigative journalism - to identify possible corruption based upon the relationships between people, companies, and most importantly financial institutions.
   Example - https://neo4j.com/graphgists/the-panamapapers-example-dataset-president-of-azerbaijan/
3. Network and IT operations - help them understand how information flows through a system and how components of a network are related. This is useful for planning, analysis of costs, and also to troubleshoot problems when a problem arises. Example - https://neo4j.com/graphgists/network-dependency-graph/
4. Transportation and logistics - Helps in supply chain management.

**Property Graphs**
  - The key elements a property graph and why they are useful.
  - How Neo4j implements a native graph using index-free adjacency.
  - Some of the ways that Relational and other NoSQL databases can be implemented in Neo4j.

**What is a Property Graph?** - Recall that nodes are the graph elements that represent the things in our data. We can use two additional elements to provide some extra context to the data.
  - Labels
       - By adding a label to a node, we are signifying that the node belongs to a subset of nodes within the graph.
       - provide a starting point for a Cypher statement.
       - In Neo4j, a node can have zero, one, or many labels.
  - Properties
      - Properties are key, value pairs and can be added or removed from a node as necessary. Property values can be a single value or list of values
      - Properties do not need to exist for each node with a particular label. If a property does not exist for a node, it is treated as a null value.

**Relationships** - A relationship in Neo4j is a connection between two nodes.
Relationship Direction 
  - In Neo4j, each relationship must have a direction in the graph.
  -  the relationship can be queried in either direction, or ignored completely at query time.
  -  A relationship is created between a source node and a destination node, so these nodes must exist before you create the relationship.
  -  Each relationship in a neo4j graph must have a type (allows us to choose at query time which part of the graph we will traverse).
    ```
    // traverse the Michael node to return the Sarah node
    MATCH (p:Person {firstName: 'Michael'})-[:MARRIED_TO]-(n) RETURN n;
    
    // traverse the Michael node to return the Graph Inc node
    MATCH (p:Person {firstName: 'Michael'})-[:WORKS_AT]-(n) RETURN n;
    
    // traverse all relationships from the Michael node
    // to return the Sarah node and the Graph Inc node
    MATCH (p:Person {firstName: 'Michael'})--(n) RETURN n
    ```

Relationship properties - As with nodes, relationships can also have properties. These can refer to a cost or distance in a weighted graph or just provide additional context to a relationship.
URL - https://graphacademy.neo4j.com/courses/neo4j-fundamentals/2-property-graphs/1-property-graph/images/relationship-properties.jpg

**Native Graph Advantage**

Neo4j is a native graph database, meaning that everything from the storage of the data to the query language have been designed specifically with traversal in mind.
Neo4j is ACID compliant.

**Index-free adjacency (IFA)** - One of the key features that makes Neo4j graph databases different from an RDBMS is that Neo4j implements index-free adjacency. Explore more on this.

**Neo4j storage** - With index-free adjacency, Neo4j stores nodes and relationships as objects that are linked to each other via pointers.

**Non-graph Databases to Graph**

Benefit of Neo4j over Relational 
  - index-free adjacency is a huge differentiator between relational and graph databases.
  - While relationships are stored at write-time in a graph database, the joins made in a relational database are computed at read-time. This means that, as the number of records in a relational database increases, the slower the query becomes. 






      - 

