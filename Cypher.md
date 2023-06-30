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





