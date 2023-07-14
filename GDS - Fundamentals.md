# Neo4j Graph Data Science Fundamentals

## Neo4j GDS Overview

we will provide a high-level technical overview of the Neo4j Graph Data Science (GDS) library. 
In this module, we will cover two core areas:

  - Product Overview - How GDS is packaged, how to install it, and licensing considerations
  - How GDS Works and Configuration GDS - GDS general workflow along with concurrency and memory configuration

### GDS Plugin and Compatibility
GDS is delivered as library and a plugin to the Neo4j Graph Database. This means that it needs to be installed as an extension in conjunction 
with configuration updates.

GDS also comes in both a free Community and paid Enterprise license.you can count on the latest version of GDS supporting the latest version of 
Neo4j and vice versa, and we recommend you always upgrade to that combination.

if you are using AuraDS, GDS Enterprise comes prepackaged and ready to use out-of-the-box. 

Of all the on-prem installations, Neo4j Desktop has the simplest process for GDS installation. if you plan on testing GDS locally on your desktop, 
Neo4j Desktop is usually the easiest place to start. Once you install and open Neo4j Desktop, you will find GDS in the Plugins tab of a database:

<img src="https://graphacademy.neo4j.com/courses/gds-product-introduction/1-neo4j-gds-overview/1-product-overview/images/gds-desktop-install-v2.png"/>

Installation for other modes please visit below link
https://neo4j.com/docs/graph-data-science/current/installation/

### Licensing
GDS has both a community and enterprise license. Both have access to all the algorithms and machine learning methods, but the enterprise version has additional 
features that enable production use cases:
  - Enterprise features for increased performance: unlimited concurrency to speed up compute time and access to a low-memory analytics graph format enabling the
    application of data science to very large graphs
  - Enterprise features for security and workflow in production: fine-grained security, the ability to persist and publish machine learning models, in-memory
    graph back-up and restore, and causal cluster compatibility via read replica

## How GDS Works

At a high-level, GDS works by transforming and loading data into an in-memory format that is optimized for high-performance graph analytics.
GDS provides graph algorithms, feature engineering, and machine learning methods to execute on this in-memory graph format. 
In this lesson we will cover the high-level workflow in GDS, as well as CPU and memory configuration to support that workflow.

### General Workflow
Below is diagram illustrating the general workflow in GDS, which breaks out into 3 high-level steps
<img src="https://graphacademy.neo4j.com/courses/gds-product-introduction/1-neo4j-gds-overview/2-how-it-works/images/gds-workflow.png"/>

  - Read and Load the Graph: GDS needs to read data from the Neo4j database, transform it, and load it into an in-memory graph. In GDS we refer to this process as
    projecting a graph and refer to the in-memory graph as a graph projection. GDS can hold multiple graph projections at once and they are managed by a component
    called the Graph Catalog. We will go over the graph Catalog and graph projection management in more detail in the next module.
  - Execute Algorithms: This includes classic graph algorithms such as centrality, community detection, path finding, etc. It also includes embeddings, a form of
    robust graph feature engineering,as well as machine learning pipelines.
  - Store Results: There are a few things you may want to do with the output/result of graph algorithms. GDS enables you to write results back to the database,
    export to disk in csv format, or stream results into another application or downstream workflow.


## Graph Catalog

The graph catalog is a concept that allows you to manage graph projections in GDS. This includes

  - creating (a.k.a projecting) graphs
  - viewing details about graphs
  - dropping graph projections
  - exporting graph projections
  - writing graph projection properties back to the database

How the Graph Catalog Works
You can call graph catalog operations with commands of the form
```
CALL gds.graph.<command>
```

Example 
1. we can list the graph projections that currently exist in our database with command ```CALL gds.graph.list()```
   Creating a projection ```CALL gds.graph.project('my-graph-projection', ['Actor','Movie'], 'ACTED_IN')```. Now run the above command to give the graph projections.

The purpose of creating a projection is to provide a space for running graph algorithms and doing graph data science efficiently.

**Running Algorithms**
As a simple example of a graph algorithm, we will run degree centrality on Actor nodes. this will count the number of movies each actor was in and store it on a node property called numberOfMoviesActedIn inside the projection (it will not be written back to the database yet).
```
CALL gds.degree.mutate('my-graph-projection', {mutateProperty:'numberOfMoviesActedIn'})
```

**Streaming and Writing Node Properties**

There will be times when we want to take the results from our algorithm calculations and either stream them into another process or write them back to the database.
```
CALL gds.graph.nodeProperty.stream('my-graph-projection','numberOfMoviesActedIn')
YIELD nodeId, propertyValue
RETURN gds.util.asNode(nodeId).name AS actorName, propertyValue AS numberOfMoviesActedIn
ORDER BY numberOfMoviesActedIn DESCENDING, actorName LIMIT 10
```
If we instead wanted to write the property back to the database we could use the nodeProperties.write operation.
```
CALL gds.graph.nodeProperties.write('my-graph-projection',['numberOfMoviesActedIn'], ['Actor'])
```
We could then query the top 10 most prolific actors by movie count with Cypher.
```
MATCH (a:Actor)
RETURN a.name, a.numberOfMoviesActedIn
ORDER BY a.numberOfMoviesActedIn DESCENDING, a.name LIMIT 10
```
## Exporting Graphs

The graph catalog has two methods for export:
  1. gds.graph.export to export a graph into a new database - effectively copying the projection into a separate Neo4j database
  2. gds.beta.graph.export.csv to export a graph to csv files

## Dropping Graphs

```
CALL gds.graph.drop('my-graph-projection')
```

## Native Projections

There are 2 primary types of projections in GDS, native projections and cypher projections. In summary, native projections are optimized for efficiency and performance to support graph data science at scale. Cypher projections are optimized for flexibility and customization to support exploratory analysis, experimentation, and smaller graph projections.

**About Native Projections**

When you call gds.graph.project() you are using a native projection. Native projections provide the best performance by reading from the Neo4j store files directly. We recommend them for both development and production phases.

The native projection takes three mandatory arguments: graphName, nodeProjection and relationshipProjection. In addition, the optional configuration parameter allows us to further configure the graph creation.

**Basic Native Projections**

Example where we project the User and Movie nodes with the RATED relationship. 
```
CALL gds.graph.project('native-proj',['User', 'Movie'], ['RATED']);
```
A GRAPH WITH NAME 'NATIVE-PROJ' ALREADY EXISTS.
```
CALL gds.graph.drop('native-proj');
```
then re run the code

The wildcard character '*' can be used to include all nodes and/or relationships in the database. 
```
CALL gds.graph.project('native-proj','*', '*');
```

## Changing Relationship Orientation

Native projections allow you to change the relationship orientation as well. To accommodate this there are three orientation options we can apply to relationship types in the relationshipProjection:
  - NATURAL: same direction as in the database (default)
  - REVERSE: opposite direction as in the database
  - UNDIRECTED: undirected

```
CALL gds.graph.drop('native-proj', false);

//replace with a project that has reversed relationship orientation
CALL gds.graph.project(
    'native-proj',
    ['User', 'Movie'],
    {RATED_BY: {type: 'RATED', orientation: 'REVERSE'}}
);

CALL gds.degree.mutate('native-proj', {mutateProperty: 'ratingCount'});
```

we want to count the number of user ratings each movie has. 
```
CALL gds.graph.nodeProperty.stream('native-proj','ratingCount', ['Movie'])
YIELD nodeId, propertyValue
RETURN gds.util.asNode(nodeId).title AS movieTitle, propertyValue AS ratingCount
ORDER BY movieTitle DESCENDING LIMIT 5
```

**Including Node and Relationship Properties**

Node and relationship properties may be useful to consider in graph analytics. They can be used as weights in graph algorithms and features for machine learning.
```
CALL gds.graph.drop('native-proj', false);

CALL gds.graph.project(
    'native-proj',
    ['User', 'Movie'],
    {RATED: {orientation: 'UNDIRECTED'}},
    {
        nodeProperties:{
            revenue: {defaultValue: 0}, // (1)
            budget: {defaultValue: 0},
            runtime: {defaultValue: 0}
        },
        relationshipProperties: ['rating'] // (2)
    }
);
```

**Notes:**
  - the defaultValue parameter allows us to fill in missing values with a default. In this case we use 0.
  - simpler syntax with no default values as these should not be missing according to the data model.

## Parallel Relationship Aggregations

The Neo4j database allows you to store multiple relationships of the same type and direction between two nodes. These are colloquially known as parallel relationships. Consider a graph of financial transaction data where users send money to one another. If a user sends money to the same user multiple times this can form multiple parallel relationships.

<img src="https://graphacademy.neo4j.com/courses/gds-product…ve-projections/images/parallell-relationships.png" />

Sometimes you will want to aggregate these parallel relationships into a single relationship in preparation for running graph algorithms or machine learning. 
Other times we may want to weight the connection between two nodes higher if more parallel relationships exists, but it’s not always easy to do so without aggregating the relationships first depending on which algorithm you use.

```
CALL gds.graph.project(
  'user-proj',
  ['User'],
  {
    SENT_MONEY_TO: { aggregation: 'SINGLE' }
  }
);
```
We can create a property with the count of the relationships as well - like so:
```
CALL gds.graph.project(
  'user-proj',
  ['User'],
  {
    SENT_MONEY_TO: {
      properties: {
        numberOfTransactions: {
          // the wildcard '*' is a placeholder, signaling that
          // the value of the relationship property is derived
          // and not based on Neo4j property.
          property: '*',
          aggregation: 'COUNT'
        }
      }
    }
  }
);
```

We can also take the sum, min or max of relationship properties during aggregation. Below is an example with sum
```
CALL gds.graph.project(
  'user-proj',
  ['User'],
  {
    SENT_MONEY_TO: {
      properties: {
        totalAmount: {
          property: 'amount',
          aggregation: 'SUM'
        }
      }
    }
  }
);
```

# Cypher Projections

While the native projection is scalable and fast, its filtering and aggregation capabilities aren’t as flexible as Cypher.Cypher projections are intended to be used in exploratory analysis and developmental phases where additional flexibility and/or customization is needed.

While Cypher projections offer more flexibility and customization, they have a diminished focus on performance relative to native projections and as a result won’t perform as quickly or as well on larger graphs. This is a key trade-off to keep in mind whenever you consider using Cypher projections.

A Cypher projection takes three mandatory arguments: graphName, nodeQuery, and relationshipQuery. In addition, the optional configuration parameter allows us to further configure graph creation.

**Applied Example**

we wanted to know which actors are the most influential in terms of the number of other actors they have been in recent, high grossing, movies with.
For the sake of this example, we will call a movie “recent” if it was released on or after 1990, and high-grossing if it had revenue >= $1M.

```
CALL gds.graph.project.cypher(
  'proj-cypher',
  'MATCH (a:Actor) RETURN id(a) AS id, labels(a) AS labels',
  'MATCH (a1:Actor)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(a2)
   WHERE m.year >= 1990 AND m.revenue >= 1000000
   RETURN id(a1) AS source , id(a2) AS target, count(*) AS actedWithCount, "ACTED_WITH" AS type'
);
```
```
CALL gds.degree.stream('proj-cypher',{relationshipWeightProperty: 'actedWithCount'})
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name, score
ORDER BY score DESC LIMIT 10
```


