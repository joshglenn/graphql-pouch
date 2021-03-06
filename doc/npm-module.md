# Using GraphQL-Pouch as a NPM-Module

Some people may want to use GraphQL-Pouch as a NPM-Module in their own NodeJS projects. GraphQL-Pouch supports this depth level integration in your own projects to do something like this:

* Use any other NPM-Module in your implementations
* Fetch data from third-party data source e.g. RESTful services, Databases, etc.
* Mutation query Input-Validation
* Data query filters/ordering
* Data query transformations e.g. cleansing or enhancements
* Forwarding mutation commands to your Backends
* Business logic implementations
* etc.

As example How to use GraphQL-Pouch as a module fork [graphql-pouch-as-module](https://github.com/MikeBild/graphql-pouch-as-library) or just do:

```javascript
const SERVICE_PORT = process.env.SERVICE_PORT || 3000;
const ENVIRONMENT = 'mygraphql';
const ENABLE_RELAY = true;

// Object-Literal for custom function implementations
const CUSTOM_FUNCTIONS = {
  settingByKey: (ctx, input) => {
    //Fake output result to demonstrate custom functions.
    ctx.success({
      id: 'A',
      rev: 'B',
      key: input.key,
    });

  }
};

// My own schema definition
const MY_SCHEMA_DEFINITION = `
#A simple Type for demonstration.
type Setting implements Node {
  id: ID!
  rev: String
  key: String
}

type Query {
  #A custom function registration
  settingByKey(key: String!): Setting
}
`;
const express = require('express');
const expressCors = require('cors');
const expressGraphQL = require('express-graphql');

//Include GraphQL-Pouch `npm install graphql-pouch --save`
const graphqlPouch = require('graphql-pouch');

//GraphQL-Pouch schema generation from shorthand notation
const mySchema = graphqlPouch.schema(ENVIRONMENT, MY_SCHEMA_DEFINITION, ENABLE_RELAY, CUSTOM_FUNCTIONS);

const app = express();
app.disable('x-powered-by');
app.use(expressCors());

app.use('/graphql', expressGraphQL({
  schema: mySchema.schema,
  context: {environment: 'default'},
  pretty: true,
  graphiql: true,
}));

const server = app.listen(SERVICE_PORT, () => console.log(`
  GraphQL server listen on port ${server.address().port}
  GraphiQL - http://127.0.0.1:${server.address().port}/graphql
`));
```
