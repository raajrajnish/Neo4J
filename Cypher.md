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




