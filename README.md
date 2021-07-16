## _A private Community App - just like a Community itself is a facebook for users_

### High Level requirement

- Login and signup
- People can create community
- admin and other members can invite other people to join
- Each community in itself is a social network like Facebook specifically for the people in that community.


## Two workflows mainly
- Feeds
do various types of posts (including text, images, videos) in community that will show up in feed of other users, interact with the posts of other users (like, comment, share etc.)
- Messanger
User can do conversation with other users via Direct messaging or there can be various user discussion groups within that community where multiple users can do conversations (Message conference).


## Problem statement
You have been appointed to design a scalable solution for the same using Big Data Technologies with the help of the system can efficiently scale to millions/billions of users and fulfill their requests in an efficient way.

Additionally, getting insights/analytics from such an app is also another use case for the organization

Make necessary assumptions regarding the users creating different data points like posts, messages, likes, comments, shares etc.

1. Propose a suitable architecture of the system with high level details of the components involved in building the complete system. [4 Marks] 

2. Detailed document on the structure of data you would store, from data flow tier to master storage to delivery tier and how the interaction between different subcomponents of the system work. [4 Marks] 
	
3. Link of a short 5–10-minute video explaining the above submissions. 

Note. You may host the video on a google drive/youtube/anywhere and share the publicly accessible link of the video in a separate Doc/PDF file. [2 Marks]
	
	
	
----------------------------------------------------------------------------------------------------------------------------------------------------	

## _Analysis and gathering technical and non technical rquirement_ 
	
### Function requirement (Use Case)
1. A user can create their own profile.(sign up and login)
2. A user can ask other existing users to his community - invite by mail and other channel
3. A user can also ask non-existing users to join his community - invite by mail and other channel
4. Users can join multiple communities.
5. Search & See friends and other's profile
6. Add friends with in community
7. Users can post messages/photos/videos etc to their timeline and associated communities member are able to see as feeds, we can think of customize visibility for each message and default will be all associated communities.
8. The system should display posts of communities friends to the display board/timeline.
9. People can like a post.
10. People can comment over post (only text as of now).
11. People can share their friends post to common communities.
12. Activity log (audit)

### Non Functional Requirement (NFR)
1. one writes and all reads even more than 100 at a time (heavy read)
2. Fast rendering and posting (saving and fetching)
3. Lag between post and read on timeline can be upto 5-10 seconds
4. Access pattern - **
5. Global - internalization - local servers from hardware perspectives
6. Scale 
	a. number of users for posting and other actions e.g like, share or just viewing timeline ** 
	b. Number of messages are post on messenger **
	c. Number of Events/min **
	d. Number of images are posted
	e. Number of videos
	f. Number of text
	
	
### Types of Users
1. Famous - Fans Following
2. Active - daily visitors or more often live
3. Live - currently live
4. Passive - Not regular visitors
5. Inactive  or suspended
	

## Constraints
1. Assuming that there 10000 communities - there won't be any relationship between communities. A community can represented by graph data structure.
2. A big network of people as represented by a graph. Each person is a node and each friend relationship is an edge of the graph.
3. So Total graphs are 10000
4. Total number of distinct users / nodes in a community: 10000
5. Total number of distinct friend’s relationship / edges: 100 * 10000 * 10000
6. Average number of messages posted by a single user per day: 10
7. Total number of messages posted by the whole network per day: 10 * 10000 * 10000 = 100 million
8. Average number of chats posted by a single user per day on messenger: 100
9. Total number of chats posted by the whole network per day: 100 * 10000 * 10000 = 1000 million

----------------------------------------------------------------------------------------------------------------------	

## _Basic Design_
Our system architecture is divided into two parts:
1. First, the web server that will handle all the incoming requests.
2. The second database, which will store the entire person's profile, their friend relations and posts.

First, three requirements creating a profile, adding friends, posting messages are written some information
to the database. While the last operation is reading data from the database.
The system will look like this:
1. Each user will have a profile.
2. There will be a list of friends in each user profile.
3. Each user will have their own homepage where his posts will be visible.
A user can like any post of their friend and that likes will reflect on the actual message shared by his
friend.
If a user shares some post, then this post will be added to the user home page and all the other friends of
the user will see this post as a new post.

## _Bottleneck_
A number of requests posted per day is 100 million for posts and 1000 million requests for chats. Approximate some 1000 request are posted per
second. There will be an uneven distribution of load so the system that we will design should be able to
handle a few thousand requests per seconds.

## _Scalability_
Since there is, a heavy load we need horizontal scaling many web servers will be handling the requests.
In doing this we need to have a load balancer, which will distribute the request among the servers.
This approach gives us a flexibility that when the load increases, we can add more web servers to handle
the increased load.

These web servers are responsible for handling new post added by the user. They are responsible for
generating various user homepage and timeline pages. In our case, the client is the web browser,
which is rendering the page for the user.
We need to store data about user profile, Users friend list, User-generated posts, User like statues to the
posts.

Let us find out how much storage we need to store all this data. The total number of users 10 million. Let
us suppose each user is using Facebook for 5 to 6 years, so the total number of posts that a user had
produced in this whole time is approximately 20,000 million or 20 billion. Let us suppose each message
consists of 100 words or 500 characters. Let us assume each character take 2 bytes.

> Total memory required = 20 * 500 * 2 billion bytes.
> = 20,000 billion bytes
> = 20, 000 GB
> = 20 TB
> 1 gigabyte (GB) = 1 billion bytes
> 1000 gigabytes (GB) = 1 Terabytes 

Most of the memory is taken from the posts and the user profile and friend list will take nominal as
compared with the posts. We can use a relational database like SQL to store this data. Facebook and
twitter are using a relational database to store their data.

Responsiveness is key for social networking site. Databases have their own cache to increase their
performance. Still database access is slow as databases are stored on hard drives and they are slower
than RAM. Database performance can be increased by replication of the database. Requests can be
distributed between the various copies of the databases.

Also, there will be more reads then writes in the database so there can be multiple slave DB which are
used for reading and there can be few master DB for writing. Still database access is slow to we will use
some caching mechanism like Memcached in between application server and database. Highly popular
users and their home page will always remain in the cache.

There may be the case when the replication no longer solves the performance problem. In addition, we
need to do some Geo-location based optimization in our solution.

Again, look for a complete diagram in the scalability theory section.
If it were asked in the interview how you would store the data in the database. The schema of the
database can look like:


Table Users:
1. User Id
2. First Name
3. Last Name
4. Email
5. Password
6. Gender
7. DOB
8. Relationship

Table Posts:
1. Post Id
2.  Author Id
3.  Date of Creation
4.  Content

Table Friends:
1. Relation Id
2. First Friend Id
3. Second Friend Id

Table Likes:
1. Id
2. Post Id
3. User Id


User -> user details | friends | user type | relevance tags | last access time 

Add post  -> 
- Short url service (not required initially)
- post ingestion service
- Asset service (resolution, video streaming optimization)
- posts Cassandra or hbase can used for storage db
- Post service (to access posts)
- KAFKA (for other services like analytics etc for stream consumer)
- Live user service
- Post processor service
- Redis for cache
- User group service
- Timeline service
- Archival service
		
User profile ->
Timeline ->
Search service -> 
Like -> 
	- like service
	- like types
comment -> comment service 
Trends -> 
- from kafka or spark stream
- hadoop cluster - CLASSFICATION, ML, DL or NLP
- Spark cluster
- ML and DL on each post and broadcast to people of same interest.
	
	
## References : 
1. [youtube](https://www.youtube.com/watch?v=9-hjBGxuiEs)
2. [codekarle](https://www.codekarle.com/system-design/facebook-system-design.html)
	
