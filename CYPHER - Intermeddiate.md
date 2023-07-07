# Filtering Queries

In this module, you will:
  - Review basic query filtering.
  - Evaluate strings in your query filtering.
  - Retrieve data using patterns in the graph.
  - Learn how to specify multiple or optional MATCH clauses.

You can view the data model in the sandbox to the right by executing this query:
```
CALL db.schema.visualization()
```
You can view the property types for nodes in the graph by executing this query:
```
CALL db.schema.nodeTypeProperties()
```
You can view the property types for relationships in the graph by executing this query:
```
CALL db.schema.relTypeProperties()
```
You can view the uniqueness constraint indexes in the graph by executing this query:
```
SHOW CONSTRAINTS
```

**Filtering with WHERE**

NOTE - Your queries will execute faster if the graph has indexes on property values. 

**Testing Equality**
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
AND m.year = 2013
RETURN m.title
```

**Testing Inequality**
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name <> 'Tom Hanks'
AND m.title = 'Captain Phillips'
RETURN p.name
```

**Testing less than or greater than**
```
MATCH (m:Movie) WHERE m.title = 'Toy Story'
RETURN
    m.year < 1995 AS lessThan, //  Less than (false)
    m.year <= 1995 AS lessThanOrEqual, // Less than or equal(true)
    m.year > 1995 AS moreThan, // More than (false)
    m.year >= 1995 AS moreThanOrEqual // More than or equal (true)
```
**Testing Ranges**
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
AND  2005 <= m.year <= 2010
RETURN m.title, m.released
```
We can also use OR to expand the filtering to return more data as follows:

```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
OR m.title = 'Captain Phillips'
RETURN p.name, m.title
```
**Testing null property values**
```
MATCH (p:Person)
WHERE p.died IS NOT NULL
AND p.born.year >= 1985
RETURN p.name, p.born, p.died
```
And we can test if a property exists using the IS NULL predicate:
```
MATCH (p:Person)
WHERE p.died IS NULL
AND p.born.year <= 1922
RETURN p.name, p.born, p.died
```

**Testing labels or patterns?**
```
MATCH (p:Person)
WHERE  p.born.year > 1960
AND p:Actor
AND p:Director
RETURN p.name, p.born, labels(p)

or

MATCH (p:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(p)
WHERE  p.born.year > 1960
RETURN p.name, p.born, labels(p), m.title
```

**Discovering relationship types**
```
MATCH (p:Person)-[r]->(m:Movie)
WHERE  p.name = 'Tom Hanks'
RETURN m.title AS movie, type(r) AS relationshipType
```

**Testing list inclusion**
```
MATCH (m:Movie)
WHERE "Israel" IN m.countries
RETURN m.title, m.languages, m.countries
```



