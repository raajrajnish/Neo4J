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


