# A REST-ful Routing Primer

## Review of REST-ful Routing

GraphQL and Relay were built to solve a very specific set of problems

REST-ful Routing
 - given collection of records on server, there should be uniform URL and HTTP request methods to utilize that collection of records
 - C(reate) R(ead) U(pdate) D(elete)
 - gives a fixed set of URLs and Methods that map to an expected Operation
   - `/<name>` => POST => Create new record
   - `/<name>` => GET => Fetch a list of all records
   - `/<name>/:id` => GET => Fetch details on single record
   - `/<name>/:id` => PUT => Update details of record
   - `/<name>/:id` => DELETE => Delete record with given id
 - Gets wierd when you start nesting data deeper

## Shortcomings of RESTful Routing
 - Not a hardcoded set of rules but conventions that a lot of languages use
 - Nested data can get messy
  - `/user/23/posts` (give me all posts created by user 23)
 - Example of things breaking down:
  - Facebook page with a list of users related to you
    - Each user has their own data (image, name, company name, position name)
      - User table
      - Company table
      - Position table
    - What RESTful route do you use to get all companies and positions from friends?
      - Option 1
        - `/users/1/companies`
        - `/users/1/positions`
        - `/users/2/companies`
        - `/users/2/positions`
        * Thats a lot of HTTP requests
      - Option 2
        - `/users/23/friends/companies`
        - `/users/23/friends/positions`
        * Very custom but will give you all of the users friends companies and positions
      - Option 3
       - `/users/23/friends_with_companies_and_positions`
       * Very custom, not very RESTful end point
       * Could end up returning a lot of company/position data that we don't need for our request
 - RESTful conventions break down once we're dealing with highly related data
 