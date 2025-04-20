## Scalability for dummies

### Clones
* Public Servers should be hidden behind a load balancer ( as discussed in the harvard lecture ) of course this means a user may go to the same server on each request
* Even though users don't always go to the same server, they should get the same results this means we must do a few things
1. Servers should not store any user related data I.E. sessions
   1. These should be stored on external or caches ( like Redis )
2. We should make sure all servers have the same code, any code changes should go to all servers
   1. There are tools like capistrano that can automate this process
   2. We can make an image of our servers ( AWS AMI for example ) and anytime we start a new clone we can use this image

### Database
* Even if we can scale our servers out to process more requests, our database is still a single point of failure we have 2 options to increase it's efficiency
1. Optimize the database's performance
   1. Add master-slave replication, increase RAM on the machine, sharding/partitioning etc.
2. Denormalize from the beginning and do your joins in code
   1. This creates more of a burden on the application code but will allow you to change less code in the future
* Either decision will alleviate some problems but eventually the dataset will grow large, you will eventually need a cache of some kind

### Caching
* Once we've scaled our db up to handle large amounts of data we still need to worry about the speed of requests
* The best way to increase this speed is by using a caching solution. These are usually stored in memory and are therefore lightning quick
* There are generally 2 patterns of caching
1. Caching database queries
   * Whenever you run a query you cache the result, the next time you run the query you check if its in the cache first
   * This pattern has some problems including expiration of complex queries & deleting the cached results when the data changes
2. Cached Objects
   * Once the query has been executed and the objects have been created from it you can cache those
   * In this case whenever there's a change you only need to delete the cached objects
   * This also allows us to perform more asynchronous processing, where we have workers building objects to cache and the application just reads the latest

### Asynchronous Processing
* There are two general ways to implement asynchronous processing in an application
1. Doing the time consuming work in advance
   * One example of this would be Static Site Generation where you pre-render your pages and store them on some CDN
2. Handle tasks asynchronously
   * One example of this is by using some message queue (RabbitMQ for example) and have asynchronous workers pick from the queue and perform those tasks
   * In this case the front end allows the user to continue browsing and in the background polls the job to check if its done
   * This also means the main application code is not stuck on an intensive task