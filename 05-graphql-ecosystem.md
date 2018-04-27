# The GraphQL Ecosystem

## GraphQL Clients - Apollo vs Relay
Currently only useful if someone is willing to write their own queries in Graphiql

Quickly evolving technologies on front end side of GraphQL
  - going to look at popular ones
  - go through pros and cons

Need to understand what information is being provided between GraphQL server and the front end
  - this will tell us how to interface with the graphql server
  - typical ajax flow with data being passed back and forth over the wire

Response from GraphQL server is very plain, raw data
  - `data: { {user: firstName: "Alice", age: 3}}`

Request payload from GraphiQL to server
  - operationName
  - variables
  - query: same string we entered in graphiql front end

We're going to have React app tightly coupled with GraphQL client
  - client's purpose is to take requests from our React app and communicate them to GraphQL server

Three big clients in use today (for JS and GraphQL)
  - Lokka: simple as possible, basic queries, mutations, some caching
  - Apollo Client: made by Meteor JS team, balance between features and complexity
  - Relay: Very performant for mobile, by far the most complex, officially used by Facebook team


## Sidenote - Apollo Server vs GraphQL Server
We have two different GraphQL dependencies
  - Apollo is the front end client for GraphQL
  - Express GraphQL is the back end client

Express GraphQL uses the Facebook standard for how to implement a GraphQL server
  - Also not likely to have any major api changes in the future
  - large object co-locating the type definition and the resolvers together
Apollo Server implements a separation of concerns pattern (with different files)
  - Possible it will have major API changes
  - is actively being developed
  - breaks things into a types file and a resolvers file
