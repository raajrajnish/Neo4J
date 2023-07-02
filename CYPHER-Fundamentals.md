# What is Cypher?

Cypher is a query language designed for graphs.

# How Cypher is used to?

Node or Verticies (i.e. entities) is stored in the database as a graph. We represent entities as circles connected together using arrows.
We have Person and Movie nodes in our graph.

So we write out the pattern in Cypher:
  - Nodes are represented by parentheses ().
  - We use a colon to signify the label(s), for example (:Person)
  - Relationships between nodes are written with two dashes, for example (:Person)--(:Movie).
  - The direction of a relationship is indicated using a greater than or less than symbol < or > , for example (:Person)-→(:Movie).
  - The type of the relationship is written using the square brackets between the two dashes: [ and ], for example [:ACTED_IN]
  - Properties in Neo4j are key/value pairs, for example {name: 'Tom Hanks'}.

Example 
(m:Movie {title: 'Cloud Atlas'})<-[:ACTED_IN]-(p:Person)

# How Cypher works?


**MATCH and RETURN Clause**

Cypher works by matching patterns in the data. We retrieve data from the graph using the MATCH keyword. You can think of the MATCH clause as similar to the FROM clause in an SQL statement.

Example 1 - Retrieve all Person nodes from the graph assigned on the variable p. and return the values using RETURN clause.
```
MATCH (p:Person)
RETURN p
```

Example 2 - Retrieve the node which represents the Person who’s name is Tom Hanks. We can use the braces {..} to specify the key/value pair of name and Tom Hanks as the filter. As Tom Hanks is a string, we will need to place it inside single or double quotes.
```
MATCH (p:Person {name: 'Tom Hanks'})
RETURN p
```

In our Cypher statement, we can access properties using a dot notation. For example, to return the name property value using its property key p.name

```
MATCH (p:Person {name: 'Tom Hanks'})
RETURN  p.born
```
In Cypher, labels, property keys, and variables are case-sensitive. Cypher keywords are not case-sensitive.
Neo4j best practices include:
  - Name labels using CamelCase.
  - Name property keys and variables using camelCase.
  - User UPPERCASE for Cypher keywords.

**WHERE Clause**

you can filter queries. using WHERE to filter your queries is very powerful because you can add more logic to your WHERE clause. 

```
MATCH (p:Person)
WHERE p.name = 'Tom Hanks'
RETURN p.name
```
```
MATCH (p:Person)
WHERE p.name = 'Tom Hanks' OR p.name = 'Rita Wilson'
RETURN p.name, p.born
```


URL 
https://graphacademy.neo4j.com/courses/cypher-fundamentals/1-reading/1-intro-cypher/

**Finding Relationships**

We can extend the pattern in the MATCH clause to traverse through all relationships with a type of ACTED_IN to any node. Our domain model shows that the ACTED_IN relationship goes in an outgoing direction from the Person node so we can add the direction in our pattern. We often refer to this as a traversal.

```
MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->()
```

Our data model dictates that the node at the other end of that relationship will be Movie node, so we don’t necessarily need to specify the :Movie label in the node - instead we will use the variable m.

```
MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->(m)
RETURN m.title
```

If our graph had different labels, for example Television and Movie nodes this query would have returned all Television and Movie nodes that Tom Hanks acted in. That is, if we had multiple types of nodes at the end of the ACTED_IN relationships in our graph, we could make sure that we only return movies.

```
MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->(m:Movie)
RETURN m.title
```

**Filtering Queries**

this query retrieves the Person nodes and Movie nodes where the person acted in a movie that was released in 2008 or 2009:
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released = 2008 OR m.released = 2009
RETURN p, m
```

  - **Filtering by node labels** It returns the names of all people who acted in the movie, The Matrix.
    ```
    MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
    WHERE m.title='The Matrix'
    RETURN p.name
    
    or
    
    MATCH (p)-[:ACTED_IN]->(m)
    WHERE p:Person AND m:Movie AND m.title='The Matrix'
    RETURN p.name
    ```
  - **Filtering using ranges** Here we want to retrieve Person nodes of people who acted in movies released between 2000 and 2003.
    ```
    MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
    WHERE 2000 <= m.released <= 2003
    RETURN p.name, m.title, m.released
    ```
- **Filtering by existence of a property** we only want to return Movie nodes where Jack Nicholson acted in the movie, and the movie has the tagline property.
  ```
  MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
  WHERE p.name='Jack Nicholson' AND m.tagline IS NOT NULL
  RETURN m.title, m.tagline
  ```
- **Filtering by partial strings** You can specify STARTS WITH, ENDS WITH, and CONTAINS.
  ```
  MATCH (p:Person)-[:ACTED_IN]->()
  WHERE p.name STARTS WITH 'Michael'
  RETURN p.name

  or
  String tests are case-sensitive so you may need to use the toLower() or toUpper() functions to ensure the test yields the correct results.

  MATCH (p:Person)-[:ACTED_IN]->()
  WHERE toLower(p.name) STARTS WITH 'michael'
  RETURN p.name
  ```
- **Filtering by patterns in the graph** find all people who wrote a movie but did not direct that same movie.
  ```
  MATCH (p:Person)-[:WROTE]->(m:Movie)
  WHERE NOT exists( (p)-[:DIRECTED]->(m) )
  RETURN p.name, m.title
  ```
- **Filtering using lists** You can define the list in the WHERE clause. During the query, the graph engine will compare each property with the values IN the list. You can place either numeric or string values in the list, but typically, elements of the list are of the same type of data. we only want to retrieve Person nodes of people born in 1965, 1970, or 1975:
  ```
  MATCH (p:Person)
  WHERE p.born IN [1965, 1970, 1975]
  RETURN p.name, p.born

  or
  Here is the query we write to return the name of the actor who played Neo in the movie The Matrix:

  MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
  WHERE  'Neo' IN r.roles AND m.title='The Matrix'
  RETURN p.name, r.roles
  ```
- **What properties does a node or relationship have?** One way you can discover the properties for a node is to use the keys() function. This function returns a list of all property keys for a node.Discover the keys for the Person nodes in the graph by running this code
  ```
  MATCH (p:Person)
  RETURN p.name, keys(p)
  ```
- **What properties exist in the graph?** you can run this code to return all the property keys defined in the graph.
  ```
  CALL db.propertyKeys()
  ```

**Writing Data to Neo4j**

Learn how to update the graph using Cypher and the Movies example dataset. You will learn to:
  -   Use MERGE to create nodes in the graph.
  -   Use MERGE to create relationships in the graph.
  -   Create, update and remove properties for nodes and relationships in the graph.
  -   Perform conditional MERGE processing, depending on what is in the graph.
  -   Delete nodes and relationships from the graph.

**Creating nodes** - Cypher code to create nodes in the graph.
  - We use the MERGE keyword to create a pattern in the database.
  - We specify the pattern that we want to create. Usually this will be a single node or a relationship between two nodes.

  ```
  #  we want to create a node to represent Michael Caine.
  MERGE (p:Person {name: 'Michael Caine'})

  # Note that when you use MERGE to create a node, you must specify at least one property that will be the unique primary key for the node.

  # This code creates two nodes, each with a primary key property.
  
  MERGE (p:Person {name: 'Katie Holmes'})
  MERGE (m:Movie {title: 'The Dark Knight'})
  RETURN p, m
```
Using CREATE instead of MERGE to create nodes - The benefit of using CREATE is that it does not look up the primary key before adding the node. You can use CREATE if you are sure your data is clean and you want greater speed during import. We use MERGE in this training because it eliminates duplication of nodes.

**Creating Relationships** - learn how to write Cypher clauses to create relationships between existing nodes in the graph.you use MERGE to create relationships between two nodes. When you create a relationship between two nodes, it must have:
  - Type
  - Direction
```
MATCH (p:Person {name: 'Michael Caine'})
MATCH (m:Movie {title: 'The Dark Knight'})
MERGE (p)-[:ACTED_IN]->(m)

# confirm that this relationship exists as follows
# Notice also that you need not specify direction in the MATCH pattern since the query engine will look for all nodes that are connected,
# regardless of the direction of the relationship.
MATCH (p:Person {name: 'Michael Caine'})-[:ACTED_IN]-(m:Movie {title: 'The Dark Knight'})
RETURN p, m
```
**Creating nodes and relationships using multiple clauses**
```
MERGE (p:Person {name: 'Chadwick Boseman'})
MERGE (m:Movie {title: 'Black Panther'})
MERGE (p)-[:ACTED_IN]-(m)

# Note that in this MERGE clause where we create the relationships, we did not specify the direction of the relationship. By default, if you do not specify the direction when you create the relationship, it will always be assumed left-to-right.
```
**Using MERGE to create nodes and a relationship in single clause**
```
MERGE (p:Person {name: 'Emily Blunt'})-[:ACTED_IN]->(m:Movie {title: 'A Quiet Place'})
RETURN p, m
```

**Adding properties for a node or relationship** - There are two ways that you can set a property for a node or relationship.
  - 1. Inline as part of the MERGE clause - You can also set a property for a relationship inline as follows:
       ```
       # In this code, the actor, Michael Caine exists but the movie, Batman Begins does not. We find the Person node and we create the Movie node.
       MERGE (p:Person {name: 'Michael Caine'})
       MERGE (m:Movie {title: 'Batman Begins'})
       MERGE (p)-[:ACTED_IN {roles: ['Alfred Penny']}]->(m)
       RETURN p,m
       ```
  - 2. Using the SET keyword for a reference to a node or relationship -
       ```
       MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
       WHERE p.name = 'Michael Caine' AND m.title = 'The Dark Knight'
       SET r.roles = ['Alfred Penny']
       RETURN p, r, m
       ```

  Setting multiple properties - If you need to set multiple properties, you separate them with a comma (,). For example:
  ```
  MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
  WHERE p.name = 'Michael Caine' AND m.title = 'The Dark Knight'
  SET r.roles = ['Alfred Penny'], r.year = 2008
  RETURN p, r, m
  ```
**Updating Properties**
  ```
  MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
  WHERE p.name = 'Michael Caine' AND m.title = 'The Dark Knight'
  SET r.roles = ['Mr. Alfred Penny']
  RETURN p, r, m
  ```
**Removing properties** - by using the REMOVE keyword, or setting the property to null. Please note - You should never remove the property that is used as the primary key for a node.
  ```
  MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
  WHERE p.name = 'Michael Caine' AND m.title = 'The Dark Knight'
  REMOVE r.roles
  RETURN p, r, m

  or
  
  MATCH (p:Person)
  WHERE p.name = 'Gene Hackman'
  SET p.born = null
  RETURN p
  ```

**Merge Processing** - MERGE operations work by first trying to find a pattern in the graph. If the pattern is found then the data already exists and is not created. If the pattern is not found, then the data can be created.

**Customizing MERGE behavior** - at runtime that enables you to set properties when the node is created or when the node is found. We can use the ON CREATE SET or ON MATCH SET conditions, or the SET keywords to set any additional properties.
```
// Find or create a person with this name
MERGE (p:Person {name: 'McKenna Grace'})

// Only set the `createdAt` property if the node is created during this query
ON CREATE SET p.createdAt = datetime()

// Only set the `updatedAt` property if the node was created previously
ON MATCH SET p.updatedAt = datetime()

// Set the `born` property regardless
SET p.born = 2006

RETURN p
```

If you want to set multiple properties for an ON CREATE SET or ON MATCH SET clause, you separate them by commas. For example:
```
ON CREATE SET m.released = 2020, m.tagline = `A great ride!'
```
Merging with relationships
```
// Find or create a person with this name
MERGE (p:Person {name: 'Michael Caine'})

// Find or create a movie with this title
MERGE (m:Movie {title: 'The Cider House Rules'})

// Find or create a relationship between the two nodes
MERGE (p)-[:ACTED_IN]->(m)

or

MERGE (p:Person {name: 'Michael Caine'})-[:ACTED_IN]->(m:Movie {title: 'The Cider House Rules'})
RETURN p, m
```

**Deleting Data** In a Neo4j database you can delete:
  - nodes
  - relationships
  - properties
  - labels
To delete any data in the database, you must first retrieve it, then you can delete it.

**Deleting a node**
```
MERGE (p:Person {name: 'Jane Doe'})
or
MATCH (p:Person)
WHERE p.name = 'Jane Doe'
DELETE p
```

**Deleting a relationship**
```
MATCH (p:Person {name: 'Jane Doe'})-[r:ACTED_IN]->(m:Movie {title: 'The Matrix'})
DELETE r
RETURN p, m
```
**Deleting a node and its relationships**
```
MATCH (p:Person {name: 'Jane Doe'})
DETACH DELETE p
```
**Deleting labels**
```
MATCH (p:Person {name: 'Jane Doe'})
REMOVE p:Developer
RETURN p
```
**What labels exist in the graph?**
```
CALL db.labels()
```






















