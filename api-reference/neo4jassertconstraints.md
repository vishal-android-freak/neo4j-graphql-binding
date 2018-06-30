---
description: TODO
---

# neo4jAssertConstraints

In order to support the use of a `@unique` field directive, `neo4jAssertConstraints` can be used to send a Cypher query to your Neo4j instance that executes the  `apoc.schema.assert` procedure. This drops all indexes and constraints, then rebuilds them for every field that has a @unique directive, in addition to all `id` fields, on each type with a `@model` directive.

### API Reference

* `typeDefs` \(required\): Your GraphQL type definitions in [SDL format](https://www.prisma.io/blog/graphql-sdl-schema-definition-language-6755bcb9ce51/). 
* `driver`\(required\): Your Neo4j driver instance \(More info [here](https://www.npmjs.com/package/neo4j-driver)\). 
* `log` \(default: `false`\): Logs result from operation.

### Example

The following would result in the creation of an index and constraint on the name property of Person nodes in your Neo4j instance.

```text
import { neo4jAssertConstraints } from 'neo4j-graphql-binding';

const driver = neo4j.driver(
  process.env.NEO4J_URI || "bolt://localhost:7687",
  neo4j.auth.basic(
    process.env.NEO4J_USER || "neo4j",
    process.env.NEO4J_PASSWORD || "neo4j"
  )
);

const typeDefs = `
  type Person @model {
    name: String @unique
  }
`;

neo4jAssertConstraints({
  typeDefs: typeDefs,
  driver: driver,
  log: log
});
```


