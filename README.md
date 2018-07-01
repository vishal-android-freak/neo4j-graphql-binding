# Introduction

It seems that [GraphQL](https://graphql.org/) users could benefit from using __[graph databases](https://neo4j.com/developer/graph-database/) and graph database users could benefit from using GraphQL. Thankfully, much has already been done to support this direction.

As part of the [GRANDstack](https://grandstack.io/) project, [Neo4j](https://neo4j.com/) has built a GraphQL-Endpoint extension named [Neo4j-GraphQL](https://github.com/neo4j-graphql/neo4j-graphql), which provides an [auto-generated API](https://github.com/neo4j-graphql/neo4j-graphql#auto-generated-query-types) and custom database procedures for updating the schema and running operations. They have also developed the [neo4j-graphql-js](https://www.npmjs.com/package/neo4j-graphql-js) package, which converts GraphQL requests directly to Cypher requests and so does not need to depend upon the use of the Neo4j-GraphQL extension.

[Apollo GraphQL](https://www.apollographql.com/) ...  
  
[Prisma](https://www.prisma.io/) has developed a variety of [GraphQL resources](https://oss.prisma.io). Their [graphql-binding](https://www.npmjs.com/package/graphql-binding) package makes it easier to use  [schema delegation](https://dev-blog.apollodata.com/graphql-schema-delegation-9d832648c543) to pass requests made to your local GraphQL server over to a remote GraphQL API. They have also developed the [Prisma API](https://www.prisma.io/docs/reference/prisma-api/overview-ohm2ouceuj), which supports [nested mutations](https://www.prisma.io/docs/reference/prisma-api/mutations-ol0yuoz6go#nested-mutations).

### Goal

The goal of this project is to use Neo4j graph databases and Apollo GraphQL server and networking resources to develop a GraphQL API that further explores using GraphQL with graph databases. Given that Neo4j uses a property graph model, it should be interesting to develop a GraphQL API that takes advantage of [relationship properties](https://neo4j.com/docs/developer-manual/current/introduction/graphdb-concepts/#graphdb-neo4j-properties).

### Features

* Augments your schema without needing to introspect your Neo4j-GraphQL HTTP endpoint and uses the ⚡ Bolt driver for all operations. 
* Allows using the [@cypher](https://github.com/neo4j-graphql/neo4j-graphql#directives) directive from `Neo4j-GraphQL` for computed fields, query types and mutations. 
* Generates the same [query types](https://www.graph.cool/docs/reference/graphql-api/query-api-nia9nushae#query-arguments) generated by `Neo4j-GraphQL`, as well as the _query arguments_ `first`, `offset`, `orderBy,` and `filter` \(see: [Prisma Query API](https://www.graph.cool/docs/reference/graphql-api/query-api-nia9nushae#query-arguments)\). 
* Supports nested _create and connect_ mutations like those in the [Prisma Mutation API](https://www.prisma.io/docs/reference/prisma-api/mutations-ol0yuoz6go/#examples) \(update, delete, etc. in development\). 
* Generates `resolvers` for query and mutation types with a `@cypher` directive, as well as all auto-generated types. 
* Supports a [@unique](https://www.prisma.io/docs/1.4/reference/service-configuration/data-modelling-%28sdl%29-eiroozae8u/#field-constraints) field directive for node property [constraints](https://neo4j.com/docs/developer-manual/current/get-started/cypher/labels-constraints-and-indexes/) in Neo4j in order to make node selection more robust.  
* Adds a [id: ID! @unique](https://www.prisma.io/docs/1.4/reference/service-configuration/data-modelling-%28sdl%29-eiroozae8u/#system-fields) field \(if not provided\) to any type with a `@model` directive and uses the [cuid ](https://www.npmjs.com/package/cuid)package to generate id field values for all auto-generated creation mutations.

### Resources

* GRANDstack --  Build full stack graph applications with ease [https://grandstack.io/](https://grandstack.io/) 
* Neo4j-GraphQL Extension --  A GraphQL-Endpoint extension for Neo4j [https://github.com/neo4j-graphql/neo4j-graphql](https://github.com/neo4j-graphql/neo4j-graphql) 
* Using neo4j-graphql-js [https://grandstack.io/docs/neo4j-graphql-js.html](https://grandstack.io/docs/neo4j-graphql-js.html) 
* What are GraphQL Bindings? [https://www.prisma.io/blog/graphql-binding-2-0-improved-api-schema-transforms-automatic-codegen-5934cd039db1/](https://www.prisma.io/blog/graphql-binding-2-0-improved-api-schema-transforms-automatic-codegen-5934cd039db1/) 
* Open Source Prisma Resources [https://oss.prisma.io](https://oss.prisma.io/)/ 
* Apollo Server 2.0 [https://www.apollographql.com/docs/apollo-server/v2/](https://www.apollographql.com/docs/apollo-server/v2/) 
* Apollo Link -- Composable networking for GraphQL [https://www.apollographql.com/docs/link/](https://www.apollographql.com/docs/link/) 







