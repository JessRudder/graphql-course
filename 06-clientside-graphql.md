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


## Apollo Client Setup


## React Component Design


## QGL Queries in React


## Bonding Queries with Components
