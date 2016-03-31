1.Explain, generally, what is meant by a NoSQL database.

NoSQL database, also called Not Only SQL, is an approach to data management and database design that's useful for very large sets of distributed data.
NoSQL, which encompasses a wide range of technologies and architectures, seeks to solve the scalability and big data performance issues that relational databases weren’t designed to address. NoSQL is especially useful when an enterprise needs to access and analyze massive amounts of unstructured data or data that's stored remotely on multiple virtual servers in the cloud.

Contrary to misconceptions caused by its name, NoSQL does not prohibit structured query language (SQL). While it's true that some NoSQL systems are entirely non-relational, others simply avoid selected relational functionality such as fixed table schemas and join operations. For example, instead of using tables, a NoSQL database might organize data into objects, key/value pairs or tuples.

Arguably, the most popular NoSQL database is Apache Cassandra. Cassandra, which was once Facebook’s proprietary database, was released as open source in 2008. Other NoSQL implementations include SimpleDB, Google BigTable, Apache Hadoop, MapReduce, MemcacheDB, and Voldemort. Companies that use NoSQL include NetFlix, LinkedIn and Twitter.

2.Explain Pros & Cons in using a NoSQL database like MongoDB as your data store, compared to a traditional Relational SQL Database like MySQL.

Pros:
1- As a rule of thumb you can find a NoSQL database that fits exactly your specific needs.
2- If you're lucky your NoSQL database could be a very simple one in terms of architecture. So you can easily operate it with your own hands without an expensive DBA, plus you may commit changes to this database code manually.
3- It is easier to find a cluster database among NoSQL ones rather than among SQL ones. A cluster database means that you install it over many machines and it's working across all those machines as if it was the only one machine

Cons:
1- NoSQL databases haven't been around here for so long. They might have some annoying bugs, poor documentation or unexpected behavior.
2- You obviously don't have a universal language like SQL. So be prepared to change completely your app code base as you're moving towards another NoSQL solution.
3- Sometimes it is not easy to find support for a NoSQL DB, even if you're ok to pay for it.

3. Explain how databases like MongoDB and redis would be classified in the NoSQL world
MongoDB is an open source database that uses a document-oriented data model.
MongoDB is one of several database types to arise in the mid-2000s under the NoSQL banner. Instead of using tables and rows as in relational databases, MongoDB is built on an architecture of collections and documents. Documents comprise sets of key-value pairs and are the basic unit of data in MongoDB. Collections contain sets of documents and function as the equivalent of relational database tables. 

Like other NoSQL databases, MongoDB supports dynamic schema design, allowing the documents in a collection to have different fields and structures. The database uses a document storage and data interchange format called BSON, which provides a binary representation of JSON-like documents. Automatic sharding enables data in a collection to be distributed across multiple systems for horizontal scalability as data volumes increase.

MongoDB was created by Dwight Merriman and Eliot Horowitz, who had encountered development and scalability issues with traditional relational database approaches while building Web applications at DoubleClick, an Internet advertising company that is now owned by Google Inc. According to Merriman, the name of the database was derived from the word humongous to represent the idea of supporting large amounts of data. Merriman and Horowitz helped form 10Gen Inc. in 2007 to commercialize MongoDB and related software. The company was renamed MongoDB Inc. in 2013. 

The database was released to open source in 2009 and is available under the terms of the Free Software Foundation's GNU AGPL Version 3.0 commercial license. At the time of this writing, among other users, the insurance company MetLife is using MongoDB for customer service applications, the website Craigslist is using it for archiving data, the CERN physics lab is using it for data aggregation and discovery and the The New York Times newspaper is using MongoDB to support a form-building application for photo submissions.

4. Explain reasons to add a layer like Mongoose, on top on of a schema-less database like MongoDB

Mongoose is a Node.js library that provides MongoDB object mapping similar to ORM with a familiar interface within Node.js. If you aren't familiar with Object Relational Mapping (ORM) or, Object Data Mapping (ODM) in the case of Mongoose, this means is that Mongoose translates data in the database to JavaScript objects for use in your application. Let's take a look at creating and storing documents in a collection using MongoDB and Mongoose.

Note: this post assumes you have Node.js and MongoDB installed. You must have MongoDB running in order to run the provided examples.
Connecting to MongoDB

For this post, we will be connecting to the test database that MongoDB defaults to when you start up the shell. We'll also make sure that any connection errors are written to the console. Note the use of the open event - this is where you will create schemas and compile models.

var mongoose = require('mongoose');

var db = mongoose.connection;

db.on('error', console.error);
db.once('open', function() {
  // Create your schemas and models here.
});

mongoose.connect('mongodb://localhost/test');

Schemas and Models

To begin, we will need a schema and a model in order to work with the data that will be persisted in our MongoDB database. Schemas define the structure of documents within a collection and models are used to create instances of data that will be stored in documents. We will be making a database of movies to keep track of which movies have credit cookies (post-credit scenes). Let's start off by creating a Movie schema and model.

var movieSchema = new mongoose.Schema({
  title: { type: String }
, rating: String
, releaseYear: Number
, hasCreditCookie: Boolean
});

// Compile a 'Movie' model using the movieSchema as the structure.
// Mongoose also creates a MongoDB collection called 'Movies' for these documents.
var Movie = mongoose.model('Movie', movieSchema);

Notice that the Movie model is capitalized, this is because when a model is compiled, the result is a constructor function that is used to create instances of the model. The instances created from the model constructor are documents which will be persisted by Mongo.
Create, Retrieve, Update, and Delete (CRUD)

Creating a new Movie document is easy now that you have defined the model, just instantiate a Movie model. Updating and saving is equally easy, make your changes then call the save function on your document.

var thor = new Movie({
  title: 'Thor'
, rating: 'PG-13'
, releaseYear: '2011'  // Notice the use of a String rather than a Number - Mongoose will automatically convert this for us.
, hasCreditCookie: true
});

thor.save(function(err, thor) {
  if (err) return console.error(err);
  console.dir(thor);
});

The save function will provide the newly created document, you can see this in what is logged to the console.

{
  "title": "Thor",
  "rating": "PG-13",
  "releaseYear": 2011,
  "hasCreditCookie": true,
  "_id": "519a979e918d4d0000000001",
  "__v": 0
}

Retrieving an existing document is done in a couple of different ways. You can find documents based on any property from the schema and you can find any number of documents - use findOne to narrow the results to a single document.

// Find a single movie by name.
Movie.findOne({ title: 'Thor' }, function(err, thor) {
  if (err) return console.error(err);
  console.dir(thor);
});

// Find all movies.
Movie.find(function(err, movies) {
  if (err) return console.error(err);
  console.dir(movies);
});

// Find all movies that have a credit cookie.
Movie.find({ hasCreditCookie: true }, function(err, movies) {
  if (err) return console.error(err);
  console.dir(movies);
});

Mongoose also allows you to easily create static helper functions for finding data.

movieSchema.statics.findAllWithCreditCookies = function(callback) {
  return this.find({ hasCreditCookie: true }, callback);
};

// Use the helper as a static function of the compiled Movie model.
Movie.findAllWithCreditCookies(function(err, movies) {
  if (err) return console.error(err);
  console.dir(movies);
});

That's all it takes to CRUD data from MongoDB - Mongoose makes it dead simple so you can start building services quickly. Using this, along with your Express skills, you can build a pretty nice web app for tracking movies with credit cookies.


5.Explain, using relevant examples, the strategy for querying MongoDB (all CRUD operations)

6.Demonstrate, using a REST-API, how to perform all CRUD operations on a MongoDB

7.Explain the benefits from using Mongoose, and provide an example involving all CRUD operations

7.Explain how redis "fits" into the NoSQL world, and provide an example of how to use it.

8.Explain, using a relevant example, how redis (or a similar) can increase scalability (drastic) for a server using server side sessions

9.Explain, using a relevant example, a full MEAN application including relevant test cases to test the REST-API (not on the production database)
