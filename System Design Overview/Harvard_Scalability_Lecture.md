### Harvard Scalability Lecture

#### Web Hosts - What to think about when choosing a web hosts for scalability
* Encryption ( SFTP vs FTP )
* Shared Hosts - Multiple websites shared on the same machine competing for resources

#### Alternatives to Web Hosting 
* VPSes
  * Virtual Private Server
  * You may still be hosted on the same machine but you're in a private virtual environment ( virtual machine )
  * This means for all intents you get your own resources
  * Resources are technically "shared" but you get your own slice
  * Data may be more secure from other customers, but not the web hoster
  * Generally more $$$
  * Amazon EC2 is an example of this charging some # of cents per minute and scaling automatically

#### How to scale a site so it can handle more users?
* Vertical Scaling
  * Increase the resources on the machine ( RAM, CPU etc.)
  * You're restricted in this case by how good a machine can be
  * As machines increase in capability ( more cores etc.) providers are able to create scaling machines
  * You can also increase hard-drive space. Using increased disk drives or SSD's
* Horizontal Scaling
  * Instead of hitting the state of the art ceiling we can use less powerful but more machines and save $$$
  * The problem in this case is we need a way to route all of the requests to our website to these multiple servers effectively
  * To do so we need to utilize a ***load balancer***
    * Instead of directly returning the IP address of the domain name we return the IP of the load balancer and let the load balancer route to the correct server
    * Now we can use private IP addresses for our servers instead of public ones
      * The rest of the world can't see private IP's
      * The world is running out of v4 IP addresses

#### Load Balancer
  * How do load balancers decide which to route
    * You can make this decision based on a number of factors such as CPU cycles etc.
      * Under this model each server must have the exact same content, meaning we're using n times resources
    * We could also have dedicated servers that handle specific things and the load balancer routes to those based on content
      * We could do this by using different hostnames ( image.blah.com etc.)
    * We could also avoid an actual "load balancer" and have the DNS act as one, returning different server IP addresses for request
      * E.G. round-robin ip 1 -> 2 -> 3 -> 1
      * Google for example partially does this
      * This can be a poor solution because some servers could get more intensive requests than others
      * Caching can also poorly balance the requests, browser, operating system etc.
  * Multiple servers can break session state
    * Generally sessions are saved as text files on specific servers
    * One possibility is to create a file server specifically for session data 
    * We could also store the session on the load balancer itself
    * This introduces possibility of losing session state however from a single point of failure, one solution is to use **RAID**
      * **Redundant Array of Independent Disks ( RAID0, RAID1, RAID5 etc. )**
        * There are generally two types stemming from RAID0 and RAID1: Striping and Mirroring
          * RAID0: Sum number of separate hard drives that get written to in stripe format
            * Stripe in this case meaning the OS writes to hard drive A a bit then B then back to A etc.
            * Striping speeds up the time it takes to write to disk
          * RAID1: In this case we mirror the data to two different hard drives
  * There are generally two ways to maintain session state
    * Shared Storage
      * Multiple ways to store session state in such a way multiple servers can access it ( mysql etc.)
      * We can create redundancy to avoid a single point of failure
    * Session Cookies
      * Generate a cookie that contains session data and store it on the browser
      * For example we could say what server we were on
      * Rather we could store a big number and its the load balancers jobs to figure out where to go
  * Nowadays there are many software implementations of load balancers
    * Elastic Load Balance (AWS), HAProxy are two examples

#### Caching
 * SQL Cache
   * DB cache
 * Memcached
   * RAM server cache
   * Eventually if cache exceeds ram, you can remove old data
   * We need to properly update the cache items when they get hit

#### Replication
 * Generally we have a master database the gets read and writes
 * The master has n slaves that data gets copied to
   * If the master dies we have redundancy
 * This also increase ***performance*** by forcing reads to certain slaves and writes to master
   * We can still maintain redundancy by having multiple masters
   * Reads can go through another load balancer
 * In this case a load balancer is a single point of failure
   * We can create redundancy by having two load balancers
   * It can operate in active-active where two load balancers receive traffic and send heartbeats to each otehr
   * Or active-passive where one receives traffic and if it dies the other gets the traffic
 * We can also ***partition*** the data
   * Such as putting certain users by last name on certain dbs
 * High Availability ( HA )
   * The term for all of this, databases or load balancers taking over if one goes down

#### Security
 * We should only allow certain amounts of network requests to our web hosts
   * tcp:80, 443, ssh etc.
 * Internal traffic can be unencrypted
 * 






















