# Fetching Data with Queries

## Nested Queries
Add anoterher type (this time CompanyType)
  - make sure it's above User (this will be explained later)
Treat associations between types as if they're just another field on the type you're working on
  - define type
    - this will take the place of one of the pre-defined types
    - user defined and pre-defined types all work the same way
  - define resolve function

## More on Nested Queries
After defining a nested association
  - need to let graphql how to take the main object and find the associated object
We call the associated field "company" not "companyId"
  - companyId is info from the json response from the database
  - company is how we define it to graphQL
    - I've got a company, but incoming data doesn't have company, what do I do?
    - This will be handled in the resolve function!
GraphQL will not ask for data you didn't request so if you're testing, make sure to add the new data into the request

At this point, the User with the nested Company data:

```
const UserType = new GraphQLObjectType({
  name: 'User',
  fields: {
    id: { type: GraphQLString },
    firstName: { type: GraphQLString },
    age: { type: GraphQLInt },
    company: {
      type: CompanyType,
      resolve(parentValue, args) {
        return axios.get(`http://localhost:3000/companies/${parentValue.companyId}`)
          .then(resp => resp.data);
      }
    }
  }
});
```

## A Quick Breather
First real association between records is now working

Reality Land
  - users companyId now points to the id on a company

GraphQL Land
  - RootQueryType has a User
  - That can be used to find a specific user record
  - the company on that record points to a specific company

Right now all associations are set up in one direction
  - must go through a user to get a company
  - can't start with a company and find users

What happens when we make a query
  - Initial query (user w/id 23)
  - Goes to RootQuery with an args object with id of 23
  - resolve function takes us to the user object with an id of 23
  - if you then ask for company info, resolve on user points to the company object w/first argument of parentValue 
    - parentValue is node on the graph the query is coming from
    - args do not get passed through that chain from earlier on
      - these were defined in the root query and don't come through after the axios call
  - now you have the company data associated with the user

Schema data is a bunch of functions that return references to other objects in our graph
  - Each edge in our graph can be thought of as being a 'resolve function'

## Multiple RootQuery Entry Points


## Bidirectional Relations


## More on Bidirectional Relations


## Resolving Circular References


## Query Fragments


## Introduction to Mutations


## NonNull Fields and Mutations


## Do It Yourself - Delete Mutation!


## Do It Yourself - Edit Mutation!

