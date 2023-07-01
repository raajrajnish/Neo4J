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









