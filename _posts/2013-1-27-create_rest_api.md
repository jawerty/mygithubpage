---
layout: post
title: "Build REST APIs with Node.js"
category: posts
---

Recently, I have been exploring node.js along with express, a minimalistic web framework created by the rather productive TJ Holowaychuck. And even more recently, a few friends and I have been creating a RESTful service to back our future games. Seeing how this minimalistic web framework allows for such a powerful and simplistic implementation of HTTP responses and requests, it serves as the perfect support mechanism for our cloud service. Along with the MongoDB database engine, these tools come together to be quite powerful solutions running the backend of our server. The database driver, Mongoose, a beautifully written implementation of object modelling with MongoDB, is used to make the MongoDB to node.js connection. Now, if you would like to create a RESTful service with these technologies I suggest reading the directions below.

**Note: In this tutorial I assume that you already have Node.js (with NPM), Express, and Mongoose installed and ready to run**

* Getting started
* Initialize server
* Define the API
* Test the REST API

First, in the blank directory of your project, type in the following command.
    
    $ express
    // create app

Now that you have an app running express. Edit the package.json file so it resembles the following contents.

    {
      "description": "A REST API written in node.js",
      "version": "0.0.1",
      "engines": {
        "node": "0.8.x",
        "npm":  "1.1.x"
      },
      "scripts": {
        "start": "node app"
      },
      "dependencies": {
        "express": "3.0.0rc5", 
        "jade": "*",
        "mongoose":"*" 
      }
    }

      "name": "restfulservice",

After the package.json is edited to depend on our desired technologies, run the following commands in order to intialize our server.

    $ npm install

    $ node app

The output should read something like, 'Express server listening on port 3000'. If so, go to the URL http://localhost:3000 in order to open up the webpage


**Note: you can delete the /views folder if you'd like; it is not necessarily needed in this tutorial.**


Ok, now that you have a running node.js server, we can start coding the RESTful service. First, we need to Define the API. This example REST API can be used to create, read, edit, and delete posts on a blog, or other written content in the web. There will be only one endpoint, '/posts'. This endpoint will be used to POST, GET, PUT, and DELETE the posts in our MongoDB database. The delete and put methods will require a query (i.e. ?title=...) in order to be executed. First off, to get it out of the way, we want to create our posts schema in a file called database.js. In the root of your directory (where the app.js file should be), create the file database.js, which will contain the following code. 

    var mongoose = require('mongoose')
    var Schema = mongoose.Schema
        ,ObjectId = Schema.ObjectId;
    var db_url = "mongodb://localhost:27017/your_database_name"
    var db = mongoose.connect(db_url);

    var postSchema = new Schema({
        id: ObjectId,
        title: String,
        content: String
    })

    var post = db.model('post', postSchema);

The code above defines a very simple Schema which maps to the collection your posts will be stored in. The 'db_url' variable is made to point towards your local database, which will be created when you run the server. The ObjectId is a value specific to the collection. The implementation of this Schema will be show below, along with the rest of the API.

In the app.js file, were the API will be written, insert the following code at the top of the page.
    
    var mongoose = require('mongoose');

    var db = require('./database');
    var post = mongoose.model('post');

This code imports mongoose, (which should have been installed with the 'npm install' command) the database file you created named database.js, and assigns the post model defined in database.js to the variable post. This should finalize the MongoDB setup. Now the API's functionality can be programmed. Once again, the REST API we're making comprises of creating, reading, editing, and deleting posts. 


Here is the final code that defines our methods. (copy and paste into the app.js file) 

    /**

     * Module dependencies.
     */
    var mongoose = require('mongoose');
    var db = require('./database');
    var post = mongoose.model('post');
    var express = require('express')
      , routes = require('./routes')
      , user = require('./routes/user')
      , http = require('http')
      , path = require('path');

    var app = express();

    app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'jade');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
    });

    app.configure('development', function(){
      app.use(express.errorHandler());
    });

    app.post('/posts', function(req, res){
      var title = req.body.title;
      var content = req.body.content;
      var newPost = post({
        title: title,
        content: content
      });

      newPost.save();
      res.send('Post created');
    });

    app.get('/posts', function(req, res){
      post.find({}, function(err, post){
        if(post){
          res.send(post);
        }else{
          res.send('No posts in database');
        }
      })
    });

    app.put('/posts', function(req, res){
      var content = req.body.content;
      var title = req.query.title;
      post.findOne({title: title}, function(err, post){
        if (post){
          post.content = content;
          post.save();
          res.send('Post edited');
        }else{
          res.send('Post doesnt exist');
        }

      })
    })

    app.delete('/posts', function(req, res){
      var title = req.query.title;
      post.findOne({title: title}, function(err, post){
        if(post){
          post.remove()
          res.send('Post removed')
        }else{
          res.send('Post does not exist')
        }
      })

    });

    http.createServer(app).listen(app.get('port'), function(){
      console.log("Express server listening on port " + app.get('port'));
    });

The app.post('/posts') method creates the post. app.get('/posts') returns a list with all posts in the database. app.put('/posts') requires only a "content" body and a query with the title of the requested post. And finally, app.delete('/posts')deletes the post requested in the query (the post that matches the title query). To test the RESTful service, you can run the curl commands below in your terminal.


To create a post

    $ curl -i -X POST -H 'Content-Type: application/json' -d '{"title": "mytitle", "content": "Edited post"}' http://localhost:3000/posts

To read all posts

    $ curl -i -X GET http://localhost:3000/posts

To edit the content of a post

    $ curl -i -X PUT -H 'Content-Type: application/json' -d '{"content": "Edited post"}' http://localhost:3000/posts?title=mytitle

To delete a post

    $ curl -i -X DELETE http://localhost:3000/posts?title=mytitle

Now, you can use this REST API however you'd like, whether to serve a blog or a todo list. If you have any questions relating to this post, Express, or any of my projects, then please comment below, follow me on Twitter (@jawerty), or simple email me at jawerty210@gmail.com. Farewell, and happy hacking.