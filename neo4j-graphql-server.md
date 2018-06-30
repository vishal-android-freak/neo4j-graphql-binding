---
description: >-
  Neo4jGraphQLServer provides a quick way to get started using
  neo4j-graphql-binding with Apollo Server 2.0.
---

# Neo4j GraphQL Server

## Overview

`Neo4jGraphQLServer` uses [neo4j-graphql-binding](https://www.npmjs.com/package/neo4j-graphql-binding) with [Apollo Server](https://www.apollographql.com/docs/apollo-server/v2/) to make it easier to get started using the generated schema or multiple bindings.  
  
The following describes the server setup process based on the default configuration:

1. [neo4jIDL](https://neo4j-graphql-binding.gitbook.io/neo4j-graphql-binding/~/edit/drafts/-LGCUyG9i9d_inyHe6vF/api-reference/neo4jidl-1) is called to update your Neo4j-GraphQL schema. 
2. [neo4jAssertConstraints](https://neo4j-graphql-binding.gitbook.io/neo4j-graphql-binding/~/edit/drafts/-LGCUyG9i9d_inyHe6vF/api-reference/neo4jassertconstraints) is used to support a `@unique` directive by [creating constraints](https://neo4j.com/docs/developer-manual/current/get-started/cypher/labels-constraints-and-indexes/) in your Neo4j instance. 
3. [buildNeo4jTypeDefs](https://neo4j-graphql-binding.gitbook.io/neo4j-graphql-binding/~/edit/drafts/-LGCUyG9i9d_inyHe6vF/api-reference/buildneo4jtypedefs) then augments the same typeDefs provided to your Neo4j-GraphQL schema. 
4. [neo4jGraphQLBinding](https://neo4j-graphql-binding.gitbook.io/neo4j-graphql-binding/~/drafts/-LGCUyG9i9d_inyHe6vF/primary/api-reference/neo4jgraphqlbinding) is used to create a [custom GraphQL Binding](https://oss.prisma.io/content/GraphQL-Binding/04-Creating-your-own-Binding.html) over the resulting augmented typeDefs. The binding is added into your server's [context parameter](https://www.apollographql.com/docs/apollo-server/v2/api/apollo-server.html#constructor-options-lt-ApolloServer-gt), by default at the key 'neo4j', so you can access it the way you normally would access a GraphQL Binding. 
5. [buildNeo4jResolvers](https://neo4j-graphql-binding.gitbook.io/neo4j-graphql-binding/~/edit/drafts/-LGCUyG9i9d_inyHe6vF/api-reference/buildneo4jresolvers) is used to generate resolvers for any query or mutation type that was generated or that has a [@cypher directive](https://github.com/neo4j-graphql/neo4j-graphql#directives). Each resolver uses the created binding to delegate all such queries or mutations to your Neo4j-GraphQL endpoint.  
6. Finally, The augmented `typeDefs` and `resolvers` are used in setting up Apollo Server.

In order to support creating multiple bindings, this entire process is repeated for every valid configuration object passed into the `bindings` argument of `Neo4jGraphQLServer`. See the [example](https://neo4j-graphql-binding.gitbook.io/neo4j-graphql-binding/~/edit/drafts/-LGCUyG9i9d_inyHe6vF/neo4j-graphql-server#using-multiple-bindings) below for using multiple bindings.

## Quick Start

This example server setup uses only auto-generated query and mutation types.

```text
import { Neo4jGraphQLServer } from 'neo4j-graphql-server';
import { v1 as neo4j } from 'neo4j-driver';

const typeDefs = `
  type Technology @model {
    name: String! @unique
    integration: [Technology] @relation(
      name: "HAPPINESS", 
      direction: OUT
    )
  }
`;

const driver = neo4j.driver(
  process.env.NEO4J_URI || "bolt://localhost:7687",
  neo4j.auth.basic(
    process.env.NEO4J_USER || "neo4j",
    process.env.NEO4J_PASSWORD || "neo4j"
  )
);

const server = Neo4jGraphQLServer({
  typeDefs: typeDefs,
  driver: driver
});

server.listen().then( ({ url }) => {
  console.log(`🚀 Server ready at ${url}`);
});
```

If you navigate to [http://localhost:4000/](http://localhost:4000/), you should see [GraphQL Playground](https://github.com/prismagraphql/graphql-playground).

### Nested Mutation

This example uses nested create and connect mutations and takes advantage of the @unique directive to create the following graph:  
  
// TODO replace graph with variant

![Screenshot of resulting graph in Neo4j Bloom](.gitbook/assets/integrationexample.png)

Run the following mutation:

```text
 mutation {
  createTechnology(
    data: {
      name: "Neo4j",
      integration: {
        create: [
          {
            name: "GraphQL",
            integration: {
              create: [
                {
                  name: "Apollo",
                  integration: {
                    connect: [
                      {
                        name: "Neo4j"
                      }
                    ]
                  }
                }
              ]
            }
          }
        ]
      }
    }
  ) {
    id		
    name
    integration {
      id
      name
      integration {
        id
        name
        integration {
          id
          name
        }
      }
    }
  }
}
```

Result:

```text
{
  "data": {
    "createTechnology": {
      "id": "cjj0dr5i00006fgr0tfukv1tn",
      "name": "Neo4j",
      "integration": [
        {
          "id": "cjj0dr5i00007fgr05js3fg30",
          "name": "GraphQL",
          "integration": [
            {
              "id": "cjj0dr5i00008fgr06ugrjnze",
              "name": "Apollo",
              "integration": [
                {
                  "id": "cjj0dr5i00006fgr0tfukv1tn",
                  "name": "Neo4j"
                }
              ]
            }
          ]
        }
      ]
    }
  }
}
```

### Query

Now you can run the following query: 

```text
query {
  Technology(orderBy: name_desc) {
    id
    name
  }
}
```

Result:

```text
{
  "data": {
    "Technology": [
      {
        "id": "cjj0dr5i00006fgr0tfukv1tn",
        "name": "Neo4j"
      },
      {
        "id": "cjj0dr5i00007fgr05js3fg30",
        "name": "GraphQL"
      },
      {
        "id": "cjj0dr5i00008fgr06ugrjnze",
        "name": "Apollo"
      }
    ]
  }
}
```

### Full Example

...Example of every use case of @cypher directive + generated query, create mutation, custom update and delete mutation in cypher directive, mention above that ...

## API Reference

All the same arguments as Apollo Server are supported, in addition to the following:

* `typeDefs` \(required\): Your GraphQL type definitions in SDL format 
* `driver`\(required\): Your Neo4j driver instance \(More info [here](https://www.npmjs.com/package/neo4j-driver)\). 
* `calls` Configures the use of `neo4jAssertConstraints` and `neo4jIDL` during setup.
  * `assert` \(default: `true`\): A boolean control that updates the unique property constraints in your Neo4j instance. 
  * `idl` \(default: `true`\): A boolean control that updates your Neo4j-GraphQL schema. 
* `augment` Configures the use of `buildNeo4jTypeDefs` and `buildNeo4jResolvers`during setup. 
  * `typeDefs`
    * `query` \(default: `true`\) A boolean controlling the generation of query types.
    * `mutation` \(default: `true`\) A boolean controlling the generation of mutation types.
  * `resolvers`
    * `query` \(default: `true`\) A boolean controlling the generation of resolvers for query types.
    * `mutation` \(default: `true`\) A boolean controlling the generation of resolvers for mutation types. 
* `indexConfig` Configures the management of generated `id` fields.
  * `use` \(default/only: `'cuid'`\) Configures what method to use when generating id field values.  
* `localBindingKey` \(default: `'neo4j'`\): The key used when storing the created binding into the server's context object. 
* `log` \(default: `false`\): Logs results from query or mutation operations, `buildNeo4jTypeDefs`,`neo4jAssertConstraints`, and `neo4jIDL`. 
* `bindings` An object containing... 

### Full Configuration

```text
Neo4jGraphQLServer({
  typeDefs: typeDefs,
  resolvers: resolvers,
  driver: driver,
  calls: {
    assert: true, 
    idl: true
  },
  augment: {
    typeDefs: {
      query: true,
      mutation: true
    },
    resolvers: {
      query: true, 
      mutation: true
    }
  },
  log: true, 
  localBindingKey: 'neo4j'
});
```

### Using Multiple Bindings

Explain why / how  
Introduce / Explain the neo4j graphql community data server, link to things

```text
full example, community data server: neo4j-graphql twitter schema
```

## Resources

Compile all links throughout above document
