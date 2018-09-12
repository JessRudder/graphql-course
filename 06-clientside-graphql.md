# Clientside GraphQL

## The Next App
Going to venture to front end and build out another mini app
  - previous sections covered back end side
  - moving forward, we're going to concentrate on the front end side

All the code in the starter app is following exact same conventions we just learned

https://github.com/stephengrider/lyrical-graphql

## Starter Pack Walkthrough
Lyrical-GraphQL project folder
  - client folder
    - this is where we're going to be writing our front end code
  - server folder
    - this is where we have a lot of the code that we've already been writing
    - one difference is the schema file is broken out into separate files

Application is going to be a song writing application
  - song index page (where you can see list of songs)
  - song detail page will have collection of lyrics
    - users can add more lyrics over time (through text input)
    - submitted lyrics can be upvoted
  - elements that aren't reflected in mockup but we might want to add
    - back button
    - edit button

How is backend working?
  - Web page talks to webpack and graphql
    - Webpack packages up front end code
    - graphql talks to express
  - Express talks to a MongoDB database
    - we're going to set up a server hosted for free by MongoLab (sandbox!)
    - will need to update code to have our mongolab uri (server.js ~line 10)

## MongoLab Setup
Need to set up the database that's going to be hosted by MongoDB Lab
  - go to mlab.com
  - create new sandbox database
  - create a user for the database
  - copy the database access url over to your app
  - replace username/password with the db user you created earlier

Fire up the app using `npm run dev`
  - should see output that you connected to mongodb instance
  - should be able to go to localhost:4000
  - also should have localhost:4000/graphql

## Working Through the Schema
GraphQL automatically generates documentation which is really handy for getting to understand a new app
  - can walk through the documentation explorer in graphiQL to get a feel for how the data in your app is set up
  - also want a good idea of which mutations are available (how can you change the data you have access to)

Run mutation to add some data to the app:

```
mutation{
  addSong(title: "Baby Got Back") {
    id
  }
}
```

Note: For the addLyricToSong mutation, I had to update the code in `models/song.js` from `song.lyrics.push(lyric)` to `song.lyrics = [...song.lyrics, lyric]` due to changes in how new versions of `mongoose` work.

```
mutation{
  addLyricToSong(songId: "5b981b90d09f6d294a6c8720", content: "Becky! Look at her butt."){
    id
  }
}
```

Now write a query to get the lyrics for the song back.

```
{
  songs {
    id
    title
    lyrics {
      content
    }
  }
}
```

Can go to `mlab.com` and look at the records in the database directly
  - can edit inside of that too!


## Apollo Client Setup
Initial work will be done inside of `/client/index.html` in the `Root` definition
  - will need to do a bit of a refactor to set up the Apollo Client/Provider

Overall in app, React application is very small piece of th epuzzle
  - Apollo Provider (wraps React app)
  - Apollo Store (communicates directly with GraphQL server and stores data it receives back)
  - GraphQL Server (talks to the database and has the data)

Apollo Store
  - Doesn't care which client-side framework is being used to show things on the screen
  - Fairly stand alone
  - Create it and then don't touch it much

Apollo Provider
  - Takes data from the store and injects it into the application
  - Glue layer between the store and our application
  - Where we'll do most of our work

`react-apollo` is the compatibility layer that lets it know we're working with react

Very similar to how Redux works
  - create a new client
  - Note: the created ApolloClient instance assumes that a `graphql` end point is available as defined in the schema
  - if you deviate from those assumptions, you'll have to add more configuration to the setup

```
const client = new ApolloClient({});
```

Wrap the Root element in the `<ApolloProvider` and pass in the `client`

```
const Root = () => {
  return (
    <ApolloProvider client={client}>
      <div>Lyrical</div>
    </ApolloProvider>
  );
};

```

`<ApolloClient>` is a react component and we're passing in a reference to the store

## React Component Design


## QGL Queries in React


## Bonding Queries with Components
