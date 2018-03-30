# On to GraphQL

## What is GraphQL
Graph is data strcuture which contains nodes and relations between each of the nodes (referred to as edges)
  - this is the graph that graphql is referring to

Imagine you want to find a user, all their friends and the companies their friends work at
  - ask to grab the user
  - then ask to grab friends associated w/user
  - then ask to grab companies associated w/friends

To do this, write and execute a query
query {
  user(id: "23") {
    friends {
      companies {
        name
      }
    }
  }
}

## Working with GraphQL
Use GraphiQL (a prebuilt app used for development purposes to learn how GraphQL works)

`npm install --save express express-graphql graphql lodash`

express - the express server
express-graphql - connector between express and graphql
graphql - the actual graphql library
lodash - has some helpful util functions


## Registering GraphQL with Express
Focus on figuring out how to make graphql and express work together
  - Express is an HTTP server
  - Adding GraphQL makes things complicated
    - Express checks if the request involves GraphQL at all
      - if so, it passes it to GraphQL to be handled first
      - if not, handles it all HTTP like as usual
GraphQL is just one piece of the app (can be used with other components)

## GraphQL Schemas
Got error saying "GraphQL middleware options must contain a schema"
  - middleware is code that handles passing messages
  - there needs to be a schema along with the options we're passing in the basic setup

Schema
  - all of the data in the app looks like a graph
  - need to specifically tell GraphQL how the data is laid out and connected
  * this goes in the schema file
    - contains all knowledge telling GraphQL what your app data looks like


## Writing a GraphQL Schema
Schema
  - tells GraphQL what our data looks like
  - tough code, but mostly repetitive

Using 'graphql' for this
  - will be destructuring a lot of things for this

```
  const {
    GraphQLObjectType,
    GraphQLString,
    GraphQLInt
  } = graphql;
```

Declare what the fields are and what the types are:

```
const UserType = new GraphQLObjectType({
  name: 'User',
  fields: {
    id: { type: GraphQLString },
    firstName: { type: GraphQLString },
    age: { type: GraphQLInt }
  }
});
```

## Root Queries
Root Query
  - something that allows us to jump in to our graph of data
  - `args` specifies arguments that are required for the root query to return data

Resolve Function
  - part of the root query that tells you how to go into the database and find the data
    - 1st arg is parentValue (rarely used)
    - 2nd arg is args for the RootQuery

```
const RootQuery = new GraphQLObjectType({
  name: 'RootQueryType',
  fields: {
    user: {
      type: UserType,
      args: { id: { type: GraphQLString } }
      resolve(parentValue, args) {

      }
    }
  }
});
```

## Resolving with Data
Starting out with hard coded data at top of file instead of database

Going to use GraphQLSchema to take in RootQuery and return the schema


## The GraphiQL Tool
GraphQL Tool
  - Documentation Explorer
    - starts at RootQuery and shows other data available
    - shows the schemas for each data type
    - will automatically fill out as you build out your schema

Left Panel is for writing queries
  - Only returns the specific fields you request

Right panel shows result of the queries
  - Our resolve returns a raw javascript object
  - Tool turns it into nice, readable graphql data

Note 1: No errors for bad ids....just `null`
Note 2: If you don't have a required argument in the query you'll get an error

## A Realistic Data Source
# Small app setup
            Browser (GrapihQIL)
                    |
            Express/GraphQL Server
                    |
                 Database

# Large company setup
            Browser (GraphiQIL)
                    |
          Express/GraphQL Server
    |                   |                 |
Outside Server #1  Outside API  Outside Server #2
    |                                     |
Database                              Database

Have many internal systems (no monolithic database)
  - GraphQL serves as a proxy to go to all data sources and groups the data together
  - Can combine data from various DBs and Outside APIs

For project will need to setup a 2nd server to act as the outside API
  - gonna do it using JSON Server
  - npm install --save json-server
    - add a db.json file with the fake data
    - start up the database

## Async Resolve Functions
GraphiQL and JSON-server are on two separate ports
  - completely different servers

Return promise from `resolve` function
  - graphql detects this
  - waits for promise to resolve
  - then moves forward with the rest of the resolve

Will almost always give a promise to the resolve

Use axios to make http requests
  - have axios do a get request to the localhost:3000/users/${args.id} end point
  - data comes back nested under `data`
    * { data: { firstName: 'bill' }}
    - solve this with `.then(res => res.data)`

## Nodemon Hookup
Watches over project files and automatically restart the sever when projet code changes
  - ensures up-to-date project code is always running

`npm install --save nodemon`

Make separate script inside package.json to start nodemon up

`npm run dev`
  - will start up server with nodemon running
  - any time you change files in the project, the server will restart

## Company Definitions
Time to complicate things!!!!
  - hook up a company to a user

Add companies and companyId
  - now you can go to `localhost:3000/companies/1/users` and get all the users with a companyId of 1
  