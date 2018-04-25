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
We can drill deeper on a User to get to a Company/Hobby

We *cannot* start with a Company/Hobby directly

Add ability to go from RootQuery to Company directly
  - looks very similar to the setup for users in RootQuery
    - give it the name you want to access it from (company)
    - make sure the type is CompanyType
    - give it the proper URL in your database

You'll need to refresh the GraphiQL page when you update the schema

## Bidirectional Relations
Can't automatically query users through company because we haven't set up that relationship
  - can go user to company
  - we can't go company to user

Every company we have has an association of users
  - can go from a single user to a single company
  - from company, we'll need to get a list of associated users

## More on Bidirectional Relations
In the JSON server you can see the relationship that is setup by browsing a restful route in the browser
  - this is give to you automatically thanks to the plugin

Set up is similar to connecting User to Company
  - NOTE: Can't just use UserType, need to tell GraphQL that it's a list of users
  - `type: new GraphQLList(UserType)`
  - make sure to destructure `GraphQLList` from `graphql` library if you haven't already

Don't need any specific user data as you can get it through the company relationship
  - `http://localhost:3000/companies/${parentValue.id}/users`

Currently doesn't work because CompanyType is defined above UserType so you get a `UserType is not defined`
  - can't move UserType up or it will have same error when trying to refer to CompanyType
  - OH NO!!!!!

## Resolving Circular References
We have a circular reference
 - can't have CompanyType refer to UserType because it's defined later
 - can't move it because UserType needs to refer to CompanyType

Need to create a closure around the fields section
  - use 
```
  fields: () => ({
    id: {type: GraphQLString},
    ....
  })
```

```
const UserType = new GraphQLObjectType({
  name: 'User',
  fields: () => ({
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
  })
});
```

The above works means the function gets defined but isn't executed until the rest of the file is executed
  - at that point, the CompanyType, UserType and HobbyType will exist internally
  - when they get called, we can resolve the closures and get the data

Note: This is more of a javascript limitation than a graphQL limitation

## Query Fragments
You'll often see query written like this

```
query {
  company(id: "1"){
    id
    name
    description
  }
}
```

Naming query is more useful
  - allows you to reuse queries inside your codebase

```
query findCompany {
  company(id: "1"){
    id
    name
    description
  }
}
```

Opening set of curly braces == the query being sent to RootQueryType

Can ask for multiple companies in a single query
  - need to give it a name so you don't have duplicate keys coming in the query
  - results will return the fields labeled with the name you gave them

```
query findCompany {
  apple: company(id: "1"){
    id
    name
    description
  }
  google: company(id: "2"){
    id
    name
    description
  }
}
```

Query Fragments
  - allows you to get rid of duplicate code
  - list of different properties we want to get access to
    - need to declare a fragment and give it a name
    - say which Type you want the fragment to operate on
    - list the fields you want to include

```
fragment companyDetails on Company {
  id
  name
  description
}
```

  - now you can use that in the original query

```
query findCompany {
  apple: company(id: "1"){
    ...companyDetails
  }
  google: company(id: "2"){
    ...companyDetails
  }
}
```


## Introduction to Mutations
Mutations are used to change our data in some fashion
  - delete
  - update
  - create

JSON server we're using allows for editing the data using RESTful conventions

Our Schema
  - RootQuery
    - access to UserType and CompanyType
  - we're going to add a mutation that allows us to manipulate our data in similar fashion

None of this manipulation will be associated to the types we've already defined
  - No logic around editing these types will be added to the UserType or CompanyType object
  - we'll be creating other objects that contain the editing instructions

Object will start out very similarly
  - main difference will happen in the resolve function
  - fields of the mutation will describe the operation the mutation is expected to take (e.g. `addUser`)
  - type refers to type you expect to return
    - collection you're working on and type you're returning may not always be the same
  - args we're passing in to the resolve function
    - most likely the fields you expect someone to include for manipulating the record (e.g. a create should have the data used to create the record)
  - resolve is the instructions for how to create the record in the database

## NonNull Fields and Mutations
Mutations are used to change our underlying data in some fashion
  - every mutation will have a specific name that should indicate the purpose of the mutation

In regards to the args
  - some should always be present
    - like firstName and age in our present example
  - some may be optional
    - like companyId
  - wrap required fields with `new GraphQLNonNull()`
    - low level validation that asserts that a value must be passed in
    - does not do any type checking on what is being passed in

Resolve function is where we actually undergo the operation to create/change the data in our database
  - make the post request and resolve

```
const mutation = new GraphQLObjectType({
  name: 'Mutation',
  fields: {
    addUser: {
      type: UserType,
      args: {
        firstName: { type: new GraphQLNonNull(GraphQLString) },
        age: { type: new GraphQLNonNull(GraphQLInt) },
        companyId: { type: GraphQLString }
      },
      resolve(parentValue, { firstName, age }) {
        return axios.post('http://localhost:3000/users', { firstName, age })
          .then(res => res.data);
      }
    }
  }
})
```

Now, when we look at the docs in graphiql, you see the following mutation
  - the bangs indicate required fields

```
addUser(
firstName: String!
age: Int!
companyId: String): User
```

To query, you do the following
  - state that it's a mutation
  - give the mutation name
  - pass in the data
  - ask for some kind of response (should be based on what you get back from the resolve function)

Example Invocation of a Mutation
```
mutation {
  addUser(firstName: "Alice", age: 11) {
    id
    firstName
    age
  }
}
```

Example Response from that Mutation
```
{
  "data": {
    "addUser": {
      "id": "TZyUcAp",
      "firstName": "Alice",
      "age": 11
    }
  }
}
```

## Do It Yourself - Delete Mutation!


## Do It Yourself - Edit Mutation!

