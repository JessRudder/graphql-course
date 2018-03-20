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


## Writing a GraphQL Schema


## Root Queries


## Resolving with Data


## The GraphiQL Tool


## A Realistic Data Source


## Async Resolve Functions


## Nodemon Hookup


## Company Definitions