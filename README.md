# REST-API

## NodeJS ExpressJS MongoDB

We are going to build a RESTful API using Node.js and a framework called Express. Later, we will add a database to our API so the data will be available after restarting the server. Also, we will add protection to our routes and requests, to let only authorized users access different parts of the API. 

### ExpressJS
What Express is
- How to create an API with Express 
- What MongoDB is 
- How to store data in MongoDB 
- What JWT is 
- How to protect routes with JWT 

Here is a simple diagram of what our API would be. 

![](https://ibb.co/fFzHRxw)

Express.js is a popular web framework written in JavaScript. This module's job is to ease the development process and help you set up your API.

It can be installed using: 
```
$ npm install express --save
```
To use it in a module, you need to import and call it as a method like the following:
```
const express = require('express');
const app = express();
```

It is possible to write an API without using any framework (pure Node.js). However, it is recommended to use a stable framework to have an easily maintainable API that provides minimal boilerplate code.

To setup a project, create a directory and initialize the node project 
```
$ npm init
```
You can leave the options as they are during initialization, and if you need to, change them later from the package.json file.

In the end, our project will have the structure described below.
```
.
├── api
│      ├── middleware
│      │      ├── check-auth.js
│      │      └── query-validator.js
│      ├── models
│      │      ├── code.js
│      │      └── user.js
│      └── routes
│               ├── codes.js
│               └── users.js
├── app.js
├── package.json
└── server.js

4 directories, 9 files
```

In your project directory, type 

```
npm install express –save
```
in the terminal to install the Express framework. Then add the app.js and server.js files in the projects main directory.

Now that you know what Express is and you’ve installed it, let’s create the server. You may remember from the Create Web Server lesson that to create a server, we need to import the built-in ‘http’ module.

```
// server.js
Const http = require(‘http’);

Const port = process.env.PORT || 3000;
Const server = http.createServer();
Server.listen(port);
```

Notice that we have used the environment variable PORT (process.env.PORT) in the variable port. This means that Node will check for a variable ‘PORT’ in its environment variables (process.env) and if ( || ) nothing is found, then it will automatically use the port 3000.
Now we have a simple server which will handle the main part of our API service. We are going to write our first request handler using Express, which will return the same thing for any type of request.
```
// app.js
Const express = require(‘express’);
Const app = express();

App.use((req, res, next) => {
  Res.status(200).json({
    Message: ‘Hello World!’
  });
});
```
The use() method above is set up as middleware, and the incoming request goes through it. Now we are setting up a handler where it takes the arguments req (request), res (response), and next (function) where the next passes the request to the next middleware if one is available.

So, in the response, we are setting a status of 200 (Ok) to respond and pass the message as a JSON object.
There’s one thing left to do: export the app object to be able to attach it to the server.
```
Module.exports = app;
```
```
// server.js
const http = require('http');
const app = require('./app');
const port = process.env.PORT || 3000;
const server = http.createServer(app);
server.listen(port);
```
The setup above will allow us to send a request to the server and get a response of status Ok and the specified message.

To run the server, you need the following command: 
```
$ node server.js
````
It will start the server on your localhost on the port 3000 if there is no .env set.

### Users Route (GET)
We need to have all our API-specific things, such as routings, in one place. So let’s create an ‘api’ directory and a subdirectory of ‘routes’, where we will keep our ‘codes.js’ and ‘users.js’ files. So, the structure is the following:
```
.
├── api
│      └── routes
│               ├── codes.js
│               └── users.js
├── app.js
├── package.json
└── server.js

2 directories, 5 files
```


Now that we have the api/routes/users.js file, let’s create our first user route (GET) which will return all the users. The file api/routes/users.js file will contain all the routes related to users. 

Open the api/routes/users.js file and import the ‘express’ module. We need to call the Router() method on the Express instance in order to use routing functionalities:
```
// users.js
const express = require('express');
const router = express.Router();

router.get('/', (req, res, next) => {
  res.status(200).json({
    message: 'Handling GET request of the /users'
  });
});
```
get() is a method which takes two arguments. The first one is the URL and the second argument is the handler function. There is nothing new in the body of the handler, where it responds with the status of 200 in a JSON format with a custom message.

### Users Route Setup

We already added our GET route in the users.js file, but we need to fix a few things.

We know that our base URL is localhost:3000, and everything inside the users.js file should be under the localhost:3000/users, so let's set it from the app.js file where it will handle all the incoming requests from the users.js route by attaching a /users before the path itself.
```
// app.js
...
const usersRoutes = require('./api/routes/users');
```
Now, let's modify the app.use() method to receive the usersRoutes requests and attach the prefix URL (path):

```
// app.js
...
const usersRoutes = require('./api/routes/users');
app.use('/users', usersRoutes);
```
Now that we have the necessary setup in app.js file, we can add as many routes as we want in the users.js file. So, let's write the POST request handler.
```
// users.js
router.post('/', (req, res, next) => {
    res.status(200).json({
        message: 'Handling POST request of the /users'
    });
});
```
Don’t forget to export your router so the app.js file can access your users.js file:
```
// users.js
...
module.exports = router;
```
Now that we have our GET and POST routes, let's try sending a request with the Postman.
Don't forget to restart your server to apply the changes. For that, you can kill the server by pressing Ctrl+C key combination and start it again with ($ node server.js) command.

### Get User By ID
Now we want to get a single user’s data by their ID. Again we are going to use a GET request, but in this case, the route should be dynamic. For example, ‘/users/1234’ or ‘/users/admin’ etc. We want to process all such routers in a single handler.
For that we need to use a placeholder in the URL called request parameter. Express provides request parameter extraction by using req.params. The following code uses ‘:userID’ in the route and extracts it inside the callback.
```
// users.js
router.get('/:userID', (req, res, next) => {
  const id = req.params.userID;
});
```
So, here the userID is a placeholder for request parameter variable (indicated by : (colon) ), which is accessible from req.params.userID. You can use any valid name for request parameter variables.

Now let's handle the ID. For example, let's check the userID against the 'admin' value and respond properly.
```
// users.js
router.get('/:userID', (req, res, next) => {
  const id = req.params.userID;

  if (id === 'admin') {
    res.status(200).json({
      message: 'You are the Admin!',
      ID: id
    });
  } else {
    res.status(200).json({
      message: 'You are a standard user',
      ID: id
    });
  }
});
```
### Update Users
Let's complete our /users routes with PATCH and DELETE requests.
Again, we need a specific user for those actions, so we need to use "userID" in routes. This will help to modify the user by its unique ID.
```
// users.js
router.patch('/:userID', (req, res, next) => {
  const id = req.params.userID;
  // find and update user by id
  res.status(200).json({
    message: 'User updated!'
  });
});
```
The PATCH request method applies partial modifications to a resource. The HTTP PUT method only allows complete replacement of a document. PATCH is not idempotent, meaning successive identical patch requests may have different effects.
```
// users.js
router.delete('/:userID', (req, res, next) => {
  const id = req.params.userID;
  // find and delete user by id
  res.status(200).json({
    message: 'User Deleted!'
  });
});
```
### Error handling
It’s always possible for a request to be wrong, either because of human error or code bugs. Therefore, we need to improve our API and include a central error handler that will catch the main errors and won't let the server crash.

Remember that in app.js, we had a middleware to handle all the incoming requests to the /users route. Our error handling middleware will catch anything that isn't captured by that middleware or any other one (we will add this later).

First, let's handle the Not Found error. For this, we need to use the app object again and, just like any other route, get the req, res and next parameters and return a callback. This code snippet needs to be at the end of your app.js file, just before the export.
```
// app.js
app.use((req, res, next) => {
  const error = new Error('Not found');
  error.status = 404;
  next(error);
});
```
So, in the code above, we get the request and throw an error object, attach the status code 404 (which stands for Not Found), and finally pass the error object into the next().

But, so far, we don't have anything that will catch the next(). So, let's create a general error handler which will get the next() call and handle it properly.

Error handlers need one more extra parameter: error.
```
// app.js
app.use((error, req, res, next) => {
  res.status(error.status || 500);
  res.json({
    error
  });
});
```
Here we check if there is a custom status on the error, and if not, by default, it will return the status 500. The rest is similar to what we did in previous lessons, creating a custom JSON object for the response.

You are free to handle the JSON body as you wish. For example, you could choose to only get the error.message and return it as the message, for example:
```
//app.js
...
res.json({
  error: {
    message: error.message
  }
});
```
### Body parser

We need to parse the body of incoming requests. However, by default, it is not that easy to handle that, and the data is not in a pretty format. There is a package called body-parser whose job is to help us to parse the data.

By default, this library supports URL encoded and JSON data, but not files or media. To install the package use the following command: 
$ npm install --save body-parser

After the installation, you need to add it and set it up in your app.js file. First of all, let's import it at the top:
```
// app.js
...
const bodyParser = require('body-parser');
...
```
After importing, you need to set up the middleware, so it knows what kind of bodies you want to parse. You can add more than one body type. Here we will add both URL encoded and JSON bodies. You need to set them up right after importing the routes.
```
// app.js
...
 app.use(bodyParser.urlencoded({extended: false}));
...
```
Here extended means that the body can have reach data or not, and it takes true or false accordingly. It is recommended to set it false by default.

But so far we are not supporting JSON bodies, so let's add that support right after the first body parser:
```
// app.js
...
app.use(bodyParser.json());
...
```
Now, this middleware will automatically catch both types of bodies before checking the routes. So, inside the routes, we can easily extract the requests’ bodies.
```
// users.js
router.post('/', (req, res, next) => {
  const user = {
    name: req.body.name,
    age: req.body.age
  };
  res.status(201).json({
    message: 'Handling POST request of the /users',
    user: user
  });
});
```
### What is MongoDB

MongoDB is a free and open-source cross-platform document-oriented database. Classified as a NoSQL database, it uses JSON-like documents with schemas.
It has some major differences from SQL databases like MySQL and SQLite. In cases where we have few or no relations and mostly reads rather than writes, it is recommended to use NoSQL databases because they are easier to deal with, have no strict schemas, and allow different documents in the same collection. NoSQL databases are usually easier to scale horizontally, which is easier and safer than ones scaled vertically.
MongoDB has some drawbacks. It is not always the best solution, and big platforms and services usually use both NoSQL and SQL databases in different parts of their platforms.

MongoDB provides a CLI tool to create and manage the database, and different platforms use packages to work with that database. For example, Mongoose is used to work with the database directly from Node.js.
Install and Setup MongoDB
After the installation, you can use the following command to start the mongod server:
```
$ sudo service mongod start
```
To shut down server:
```
$ sudo service mongod stop
```
To start a mongo shell on the same host machine as the mongod, use the --host command line option to specify the localhost address and port that the mongod listens on.
```
$ mongo --host 127.0.0.1:27017
```
### Mongoose
Mongoose is an intermediary between MongoDB and the server (Node.js), which makes interactions with a database (create, read, write, etc.) very simple.

In other words, as it mentioned on their official website: "Mongoose provides a straight-forward, schema-based solution to model your application data. It includes built-in typecasting, validation, query building, business logic hooks and more, out of the box."

By default, it supports an asynchronous environment, so you can use its module methods just like any other tools in Node.js.
To use Mongoose, first, install the package and then require it. To install, use the following command:
```
$ npm install --save mongoose
```
To use:
```
Const mongoose = require(‘mongoose’);
```
To connect: 
```
Mongoose.connect(‘mongodb://127.0.01:27017/your_database_name’);
```
Add Database Setup
So, now that you’ve learned what MongoDB is, learned about Mongoose, and installed mongod server on your computer, it's time to add it to our project.

First of all, check if your mongod server is running. Next, install the mongoose package. Finally, connect the Mongoose to the mongod server. We need to set up the connection in our app.js file. So, first import it and then set it up as follows (you can add it just after importing the routes):
```
// app.js
mongoose.connect('mongodb://127.0.0.1:27017/solodb', (err) => {
  if (err) throw err;
  console.log('Successfully connected to .');
});
```
We used the connect method and passed the database URL. Finally, we used the callback to throw the error in case of failure and return a success message otherwise.
MongoDB Model
All the setup is done. We have a working MongoDB and Mongoose is connected to our database. Now we need to go back to our project and start writing some JavaScript again!

To be able to store data in our database, we need Mongoose models. You need to define how you are going to store objects in the database, then create a model (JavaScript Object) based on that. This model will have some functions which will help you to add, update, delete, merge, fetch, order, and perform many more operations on that dataset.

Now, let's remember our project structure from here. We need to add the "models" directory in our project so that it will look like this:
```
 .
├── api
│      ├── models
│      │      ├── code.js
│      │      └── user.js
│      └── routes
│               ├── codes.js
│               └── users.js
├── app.js
├── package.json
└── server.js

3 directories, 7 files
```
A model is just a JavaScript Object that is built over Mongoose. Let's start with the user.js file. First of all, you need to require the Mongoose. Next we need the schema:
```
// models/user.js
const mongoose = require('mongoose');

const userSchema = mongoose.Schema({
});
```
We just passed a JavaScript object to the method schema, where we should define what our model looks like.
Let's add ID, email, name, age, and country:
```
// models/user.js
const userSchema = mongoose.Schema({
  _id: mongoose.Schema.Types.ObjectId,
  email: String,
  name: String,
  age: Number,
  country: String
});
```
Now that we have the schema, we need to create a model and export it to be able to use it in other modules.

The model function takes two arguments: first, the name that we are going to use internally, and second, the schema we created:
module.exports = mongoose.model('User', userSchema);

Model for Codes
We need to create a new model for the code, just like the model we created for the user.
Overall, it is similar except for the schema itself:
```
// models/code.js
const mongoose = require('mongoose');

const codeSchema = mongoose.Schema({
    _id: mongoose.Schema.Types.ObjectId,
    language: String,
    body: String
});

module.exports = mongoose.model('Code', codeSchema);
```
This will work fine for now. However, we need some relation in our code schema to link the document to a user. So, how we will know which code is created by which user (the owner of the code)?

For that we need to add a new field in our schema, which will take the user ID referenced to the user model.
```
const mongoose = require('mongoose');

const codeSchema = mongoose.Schema({
    _id: mongoose.Schema.Types.ObjectId,
    language: String,
    body: String,
    user: { type: mongoose.Schema.Types.ObjectId, ref: 'User' }
});

module.exports = mongoose.model('Code', codeSchema);
```
Here, we set the type to the Mongoose object and link it (with keyword 'ref') to the previously created model 'User'. The names should match.
